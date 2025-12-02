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
$SonarHost = "http://localhost:9000"
$ProjectKey = "YOUR_PROJECT_KEY"
$Token = "YOUR_TOKEN_HERE"

$OutputPdf = "KVAAdmin_Report.pdf"
$HtmlFile = "KVAAdmin_Report.html"

# ==========================================
# 1. 인증 토큰 처리 (Token:)
# ==========================================
$encodedAuth = [Convert]::ToBase64String(
    [Text.Encoding]::ASCII.GetBytes("$Token:")
)

# ==========================================
# 2. Summary Metrics 가져오기
# ==========================================
$metricsUrl = "$SonarHost/api/measures/component?component=$ProjectKey&metricKeys=bugs,vulnerabilities,code_smells,coverage,duplicated_lines_density,reliability_rating,security_rating,maintainability_rating"

$summary = Invoke-RestMethod -Uri $metricsUrl -Headers @{Authorization="Basic $encodedAuth"}

# 값 정리
function Get-MetricValue($metricName) {
    $measure = $summary.component.measures | Where-Object {$_.metric -eq $metricName}
    if ($measure) { return $measure.value } else { return "N/A" }
}

$bugs = Get-MetricValue "bugs"
$vuln = Get-MetricValue "vulnerabilities"
$smells = Get-MetricValue "code_smells"
$coverage = Get-MetricValue "coverage"
$dup = Get-MetricValue "duplicated_lines_density"
$rel = Get-MetricValue "reliability_rating"
$sec = Get-MetricValue "security_rating"
$mnt = Get-MetricValue "maintainability_rating"

# ==========================================
# 3. Issues Pagination 처리
# ==========================================

$allIssues = @()
$pageSize = 500
$page = 1

Write-Host "Fetching issues…"

while ($true) {
    $issuesUrl = "$SonarHost/api/issues/search?componentKeys=$ProjectKey&p=$page&ps=$pageSize"
    $result = Invoke-RestMethod -Uri $issuesUrl -Headers @{Authorization="Basic $encodedAuth"}

    $allIssues += $result.issues
    Write-Host "  Page $page fetched: $($result.issues.Count) items"

    if ($result.issues.Count -lt $pageSize) { break }
    $page++
}

Write-Host "Total Issues Loaded: $($allIssues.Count)"

# ==========================================
# 4. Severity Count
# ==========================================
$sevBlocker = ($allIssues | Where-Object {$_.severity -eq "BLOCKER"}).Count
$sevCritical = ($allIssues | Where-Object {$_.severity -eq "CRITICAL"}).Count
$sevMajor = ($allIssues | Where-Object {$_.severity -eq "MAJOR"}).Count
$sevMinor = ($allIssues | Where-Object {$_.severity -eq "MINOR"}).Count
$sevInfo = ($allIssues | Where-Object {$_.severity -eq "INFO"}).Count

# ==========================================
# 5. HTML 템플릿 생성 (고급 리포트 스타일)
# ==========================================
$html = @"
<!DOCTYPE html>
<html>
<head>
<meta charset='UTF-8'>
<title>SonarQube Premium Report</title>

<style>
body {
    font-family: 'Segoe UI', Arial, sans-serif;
    background: #eef1f4;
    margin: 20px;
}
h1 {
    color: #0d6efd;
    margin-bottom: 5px;
}
h2 { color: #495057; }

.card {
    background: white;
    padding: 20px;
    border-radius: 12px;
    box-shadow: 0 4px 12px rgba(0,0,0,0.1);
    margin-bottom: 30px;
}

/* Summary Grid */
.summary-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 15px;
}
.summary-item {
    background: #f8f9fa;
    border-radius: 8px;
    padding: 12px 15px;
    text-align: center;
    border: 1px solid #dee2e6;
}
.summary-item b {
    font-size: 22px;
    color: #0d6efd;
}

/* Table */
table {
    width: 100%;
    border-collapse: collapse;
}
th, td {
    border: 1px solid #ccc;
    padding: 6px 10px;
    font-size: 13px;
}
th {
    background: #0d6efd;
    color: white;
}

/* Zebra Rows */
tr:nth-child(even) {
    background: #f8f9fa;
}

/* Severity Colors */
.severity-BLOCKER { background:#e91e63 !important; color:white; }
.severity-CRITICAL { background:#ff5722 !important; color:white; }
.severity-MAJOR { background:#ff9800 !important; }
.severity-MINOR { background:#2196f3 !important; color:white; }
.severity-INFO { background:#9e9e9e !important; color:white; }

</style>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
</head>

<body>

<h1>SonarQube Full Project Report</h1>
<p>Generated: $(Get-Date)</p>

<div class="card">
<h2>Summary</h2>

<div class="summary-grid">
    <div class="summary-item"><b>$bugs</b><br>Bugs</div>
    <div class="summary-item"><b>$vuln</b><br>Vulnerabilities</div>
    <div class="summary-item"><b>$smells</b><br>Code Smells</div>
    <div class="summary-item"><b>$coverage %</b><br>Coverage</div>
    <div class="summary-item"><b>$dup %</b><br>Duplications</div>
    <div class="summary-item"><b>$rel</b><br>Reliability</div>
    <div class="summary-item"><b>$sec</b><br>Security</div>
    <div class="summary-item"><b>$mnt</b><br>Maintainability</div>
</div>
</div>

<div class="card">
<h2>Severity Chart</h2>
<canvas id="severityChart" width="400" height="250"></canvas>

<script>
const ctx = document.getElementById('severityChart');
new Chart(ctx, {
    type: 'bar',
    data: {
        labels: ['BLOCKER','CRITICAL','MAJOR','MINOR','INFO'],
        datasets: [{
            data: [$sevBlocker, $sevCritical, $sevMajor, $sevMinor, $sevInfo],
            backgroundColor: ['#e91e63','#ff5722','#ff9800','#2196f3','#9e9e9e']
        }]
    },
    options: { plugins: { legend: { display: false } } }
});
</script>
</div>

<div class="card">
<h2>Issues Detail ($($allIssues.Count) items)</h2>

<table>
<tr>
    <th>Type</th>
    <th>Severity</th>
    <th>Message</th>
    <th>Rule</th>
    <th>File</th>
    <th>Line</th>
</tr>
"@

# ==========================================
# 이슈 전체 출력
# ==========================================
foreach ($issue in $allIssues) {
    $sevClass = "severity-$($issue.severity)"
    $html += "<tr class='$sevClass'>
        <td>$($issue.type)</td>
        <td>$($issue.severity)</td>
        <td>$($issue.message)</td>
        <td>$($issue.rule)</td>
        <td>$($issue.component)</td>
        <td>$($issue.line)</td>
    </tr>"
}

$html += @"
</table>
</div>

</body>
</html>
"@

# ==========================================
# HTML 저장
# ==========================================
$html | Out-File -Encoding utf8 $HtmlFile

Write-Host "HTML Generated → $HtmlFile"

# ==========================================
# 6. HTML → PDF 변환 (Chrome Headless)
# ==========================================
$chromePath = "C:\Program Files\Google\Chrome\Application\chrome.exe"

Start-Process $chromePath `
    "--headless --disable-gpu --print-to-pdf=$OutputPdf $HtmlFile" `
    -Wait

Write-Host "PDF Report Created → $OutputPdf"

```

---

## report 출력
1. PowerShell 관리자 권한 으로 실행
2. 저장 한 파일 위치로 이동 및 아래 명령어 실행
```bash
cd /설치_경로
powershell -ExecutionPolicy Bypass -File generate-report.ps1
```
