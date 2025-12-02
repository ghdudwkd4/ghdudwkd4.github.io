---
title: 웹 취약점 점검 툴 sonarQube 무료 pdf 출력 방법
layout: post
categories: backEnd
order: 5
data: 2025-12-02 15:00
---



# sonarQube 를 이용해 만든 데이터로 PDF 출력

과거 버전에서는 PDF 출력 기능을 지원하는것 같았으나, 현 시점에서는 지원하지 않음<br>
그래서 sonarQube 데이터를 api 로 받아와 chrome 을 이용해 html 로 생성 후 pdf 로 만들 예정

---

## ps 파일 생성

1. 아래 복사 후 generate-report.ps1 파일명으로 저장
```
# ===============================
# SonarQube PDF Report Generator (Chunked)
# ===============================

$SonarHost = "http://localhost:9000"
$ProjectKey = "YOUR_PROJECT"
$Token = ""
$OutputPdfPrefix = "YOUR_PROJECT_Report_Page"
$HtmlFilePrefix = "YOUR_PROJECT_Report_Page"

# -----------------------------------
# HTML Escape Function
# -----------------------------------
function Escape-Html {
    param([string]$text)
    if (-not $text) { return "" }
    return $text -replace '&','&amp;' `
                 -replace '<','&lt;' `
                 -replace '>','&gt;' `
                 -replace '"','&quot;' `
                 -replace "'","&#39;"
}

# -----------------------------------
# 인증 정보
# -----------------------------------
$encodedAuth = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes($Token + ":"))

# -----------------------------------
# 1. Summary Metrics 조회
# -----------------------------------
$metricsUrl = "$SonarHost/api/measures/component?component=$ProjectKey&metricKeys=bugs,vulnerabilities,code_smells,coverage"
$summary = Invoke-RestMethod -Uri $metricsUrl -Headers @{Authorization="Basic $encodedAuth"}

$bugs = $summary.component.measures | Where-Object {$_.metric -eq "bugs"} | Select-Object -Expand value
$vuln = $summary.component.measures | Where-Object {$_.metric -eq "vulnerabilities"} | Select-Object -Expand value
$smells = $summary.component.measures | Where-Object {$_.metric -eq "code_smells"} | Select-Object -Expand value
$coverage = $summary.component.measures | Where-Object {$_.metric -eq "coverage"} | Select-Object -Expand value
if (-not $coverage) { $coverage = "N/A" }

# -----------------------------------
# 2. Issues 전체 조회 (10,000 단위)
# -----------------------------------
$allIssues = @()
$pageSize = 10000
$offset = 0

while ($true) {
    $issuesUrl = "$SonarHost/api/issues/search?componentKeys=$ProjectKey&pageSize=$pageSize&p=$([math]::Floor($offset / $pageSize) + 1)"
    $pagedData = Invoke-RestMethod -Uri $issuesUrl -Headers @{Authorization="Basic $encodedAuth"}

    if (-not $pagedData.issues -or $pagedData.issues.Count -eq 0) {
        Write-Host "No more issues to load. Exiting..."
        break
    }

    $allIssues += $pagedData.issues
    Write-Host "Loaded $($pagedData.issues.Count) issues (Total: $($allIssues.Count))"

    $offset += $pagedData.issues.Count
    if ($offset -ge $pagedData.paging.total) { break }
}

$issuesData = [PSCustomObject]@{ issues = $allIssues }

# -----------------------------------
# 3. Chunk 단위로 HTML/PDF 생성
# -----------------------------------
$chunkSize = 1000   # 1파일당 1000건
$totalIssues = $issuesData.issues.Count
$pageCount = [math]::Ceiling($totalIssues / $chunkSize)

$chromePath = "C:\Program Files (x86)\Google\Chrome\Application\chrome.exe"

for ($i = 0; $i -lt $pageCount; $i++) {
    $start = $i * $chunkSize
    $end = [math]::Min($start + $chunkSize - 1, $totalIssues - 1)
    $chunkIssues = $issuesData.issues[$start..$end]

    $HtmlFile = "$HtmlFilePrefix$($i+1).html"
    $OutputPdf = "$OutputPdfPrefix$($i+1).pdf"

    # HTML 생성
    $html = @"
<!DOCTYPE html>
<html>
<head>
<meta charset='UTF-8'>
<title>YOUR PROJECT Report Page $($i+1)</title>
<style>
body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background: #f8f9fa; padding: 20px; }
h1 { color: #0d6efd; } h2 { color: #495057; margin-top:40px; }
.summary-card { background: white; padding: 20px; border-radius: 10px; margin-bottom: 30px; box-shadow: 0 3px 8px rgba(0,0,0,0.1); }
table { width: 100%; border-collapse: collapse; margin-top: 15px; }
th, td { border: 1px solid #dee2e6; padding: 8px 10px; font-size: 14px; }
th { background-color: #0d6efd; color: white; }
tr:nth-child(even) { background: #f1f3f5; }
.severity-BLOCKER { background:#dc3545 !important; color:white !important; }
.severity-CRITICAL { background:#fd7e14 !important; color:white !important; }
.severity-MAJOR { background:#ffc107 !important; color:black !important; }
.severity-MINOR { background:#198754 !important; color:white !important; }
.severity-INFO { background:#0dcaf0 !important; color:black !important; }
.chart-container { width: 400px; height: 300px; margin-top:20px; }
</style>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
</head>
<body>
<h1>SonarQube Project Report - Page $($i+1)</h1>
<p>Generated: $(Get-Date)</p>
<div class="summary-card">
<h2>Summary</h2>
<table>
<tr><th>Metric</th><th>Value</th></tr>
<tr><td>Bugs</td><td>$bugs</td></tr>
<tr><td>Vulnerabilities</td><td>$vuln</td></tr>
<tr><td>Code Smells</td><td>$smells</td></tr>
<tr><td>Coverage</td><td>$coverage %</td></tr>
</table>
</div>
<h2>Issues Detail</h2>
<table>
<tr><th>Type</th><th>Severity</th><th>Message</th><th>File</th><th>Line</th></tr>
"@

    foreach ($issue in $chunkIssues) {
        $type = Escape-Html $issue.type
        $sev = Escape-Html $issue.severity
        $msg = Escape-Html $issue.message
        $file = Escape-Html $issue.component
        $line = Escape-Html $issue.line

        $sevClass = "severity-$sev"
        $html += "<tr class='$sevClass'><td>$type</td><td>$sev</td><td>$msg</td><td>$file</td><td>$line</td></tr>"
    }

    $html += "</table></body></html>"

    # HTML 파일 저장
    $html | Out-File -Encoding utf8 $HtmlFile
    Write-Host "Generated HTML: $HtmlFile"

    # PDF 변환
    Start-Process $chromePath "--headless --disable-gpu --print-to-pdf=$OutputPdf $HtmlFile" -Wait
    Write-Host "Generated PDF: $OutputPdf"
}

Write-Host "All pages generated: $pageCount pages."

```

---

## report 출력
1. PowerShell 관리자 권한 으로 실행
2. 저장 한 파일 위치로 이동 및 아래 명령어 실행
```bash
cd /설치_경로
powershell -ExecutionPolicy Bypass -File generate-report.ps1
```
