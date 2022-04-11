# 개발 환경 명세

## Git

버전 관리 툴은 Git을 사용합니다. 개발 환경은 Windows와 Mac OS를 혼용하기 때문에 CRLF 이슈를 방지하기 위해 다음 설정을 해야 합니다.

```shell
# Windows 사용자
$> git config --global core.autocrlf true
# Mac OS 사용자
$> git config --global core.autocrlf input
```



## 코드 편집기 / IDE

VSCode 상에서 개발을 진행합니다. VSCode의 확장 기능은 다음을 사용합니다.

- Comment Anchors
  - 코드에 주석을 달 때 사용하는 확장 기능입니다. 특정 키워드를 사용해 살펴보아야 할 코드 라인을 쉽게 메모하고 공유할 수 있습니다.
- Git Graph
  - 개발시에 소스 버전 관리툴로 Git을 사용하는데 Git의 브랜치 상태를 보기 좋게 표시해주는 확장 기능입니다.
- Git History
  - TBD
- GitLens
  - TBD
- Prettier
  - TBD
- GitHub Pull Requests and Issues
  - 이슈와 커밋을 연결할 때 보다 적은 공수를 들게 해주는 확장 기능입니다.

## 커밋 메시지

커밋 메시지 양식은 영어를 기본으로 하며 구현 내용 기재 양식은 [링크](https://blog.ull.im/engineering/2019/03/10/logs-on-git.html) 와 동일한 양식을 따릅니다.

```
:깃모지: [이슈번호] [기능이름] 구현내용
```

## 주석 양식

JSDoc 포맷을 사용하며 내부 함수 및 모듈의 다음과 같은 형태로 주석을 답니다. 기능 및 동작을 명세하는 description에는 `@` 를 붙이지 않으며 파라미터와 리턴을 명시합니다.

```javascript
/**
 * [description]
 *
 * @param ~~
 * @returns ~~
 */
```

## 브랜치 전략

Front와 Back으로 브랜치를 나누고 이슈를 만든 다음 그 이슈를 Front 또는 Back에서 브랜치를 issue로 생성한 다음 다시 Front 또는 Back 브랜치로 merge 합니다.

약 2~3일 마다 main 브랜치로 Front 및 Back 브랜치를 merge 합니다.

코드 리뷰는 보안 담당자와 해당 이슈를 담당하지 않은 다른 사람이 리뷰를 진행하며 리뷰가 완료되면 merge 합니다.
