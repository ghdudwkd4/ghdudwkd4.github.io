---
title: git 프로젝트 올리기 혹은 많은 파일 업로드
layout: post
categories: daily
order: 3
data: 2024-10-10 18:00
---

## git bash upload

git 을 사용을 하다보면 일반 파일로 올리기도 하지만,<br>
파일의 양이 많으면 올라가지 않는다.<br>
그럴때 git bash 로 업로드 하는 방법을 적어본다.<br><br>

깃헙에 일단 빈 레포지토리가 있다는 가정에 시작한다.<br>

git bash 를 열고 작업 하던 프로젝트에 접근한다.<br>

```
cd D:
cd /home/project/login_server
```
아래와 같이 명령어를 입력한다.

```
# 초기 설정 : 아래 두가지는 안되어 있을때만 해주면 된다.
git config --global user.name "유저이름"
git config --global user.email "유저 이메일"

git init    # .git 파일 생성
git add     # . 은 선택한 프로젝트 폴더 내의 모든 파일 관리
            # 특정 파일만 하고 싶으면 git add (특정파일.파일 형식 ex: git add aaa.txt)
git status  # 상태확인
git commit -m "주석"  # 커밋

# 업로드
git remote add origin "깃헙에서 만든 레포지토리"
git push -u origin master
```

이제 깃헙 레포지토리를 확인 하면 모두 올라가있는걸 볼 수 있다.

