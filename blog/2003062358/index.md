---
layout: post
title: 코드리뷰 문화 정착하기
subtitle: 🤔 코드리뷰는 왜 중요할까? 그리고 어떻게 도입해야할까?
type: Develop
blog: true
text: true
post-header: true
comments: true
header-img: "img/about.png"
date: 2020-03-06 23:58
---



## 🧨 과거의 나에게 코드리뷰란

꽤 오랜기간 나에게 '코드리뷰'라는 것은, 오프라인 미팅에서 프로젝터를 통해 코드를 설명하며 무언가 논쟁(?)을 하는 자리였다. 어떤 날에는 분위기가 좋으면 원만하게 끝났지만, 각 참석자들이 어떤 포인트에 대해 이견이 발생하면 과도한 논쟁으로 이어지기도했었다. 때때로 그건 개인적인 개발습관 간의 차이이기도 했으므로, 점점 그 조직에서 '코드리뷰'는 불편하고 어려운 자리가 되었다. 이러한 분위기 속에서 점차 다른 사람의 코드에 대해 의견을 주기 보다는 일단 이해하려는 분위기가 형성되었으므로, 우리는 서로의 코드에 대해 터치하지 않았다.

### 🤔 뭐가 문제였을까?

#### SVN

일단 svn 환경이었기 때문에, 코드 분기나 병합 없이 여러 사람이 하나의 트렁크에서 작업을 이어나갔다. 그렇게 작업을 하다보면 언제, 누가, 어떤 기능을 개발했는지를 챙기기가 어렵다. 특히 바쁠 때에는 하루에도 몇 십개의 피쳐가 커밋되기 때문에 놓치는 업데이트들이 많아지고, 그러다보면 일일이 개발자들을 붙잡고 하나하나 설명해달라고 할 수가 없는 것이다.

#### 오프라인 코드리뷰의 번거로움

또한 pr이 가능한 git과 달리, svn에서는 별 다른 리뷰툴이 없기 때문에 오프라인으로 코드리뷰를 진행해야만 했는데, 그러다보면 개발자들이 모두 가능한 시간을 맞추기도 어려웠다. 사실 시간이 안 맞아서보다는 오프라인 미팅을 잡고 프로젝터로 코드를 공유하는데에 시간을 굳이 써야했기 때문에 다들 어지간한 건이 아니면 이를 번거로워했다.

#### 왜 리뷰는 항상 주니어의 몫인가

대개 코드리뷰는 항상 이런식으로 요청 되었다.

🙎‍♀️(시니어): 그거 어디까지 개발됐어요?

🙍‍♂️(주니어): A 기능까지 개발완료됐습니다.

🙎‍♀️(시니어): 그럼 지금 작업한데까지 내일 오전에 리뷰 한번 진행해주세요.

코드를 리뷰잉하는 것보다는 검사(?)를 받는 것에 더 포커싱이 맞춰져있는 리뷰였다. 마치 아이가 학생에게 숙제 검사 받듯이 주니어는 시니어에게 코드의 품질을 검사받았다. 과연 이 과정 속에서 시니어도 얻어가는 게 있었을까?

#### 리뷰의 대상

리뷰를 진행하다보면, 때때로 중요하지않은 논쟁들로 10분 이상 논쟁하기도 했다. 과연 지금 시점에서 그 단계까지 고민하는것이 맞는것인가? 생각이 드는 질문들, 혹은 개인의 취향에 따른 코딩 스타일들. 수개월 진행한 프로젝트에서, if else 를 한줄에 쓰니 마니 괄호를 여니 마니 하는 이야기들이 오고간다는 것은 이미 잘못되었다는 것이다.

#### 애석한 믿음

사실 같이 2-3개 프로젝트를 진행하게되면 서로의 개발 스타일을 잘 알게된다. 코드 몇 줄만 봐도 누가 짰는지가 뻔히 보이고, 그 사람이 개발한 신규 피쳐에 대해서 굳이 질문하지않아도 척척 갖다 쓸 수 있게 된다. 그러다보면 코드리뷰의 필요성을 느끼지 않게 된다. '굳이 왜?' 싶은거다.



## 🙌 코드리뷰의 중요성

#### 코드 품질 향상

다른 사람이 면밀히 내 코드를 본다는 생각에, 커밋을 하기 전 한번 더 검토하게 된다. 이는 버그를 줄여줄 뿐만 아니라 더 좋은 품질의 코드를 작성하는데에 도움이 된다. 보다 더 가독성있고, 보다 더 합리적인 코드를 작성하기 위한 고민의 시간을 늘려준다. 또한 '왜 이렇게 작성했는지'를 설명하고, '왜 이렇게 작성했을까'를 이해하는 과정 속에서 전반적인 프로젝트의 수준을 향상 시킬 수 있다.

#### 버그의 조기 발견

한 사람이 보는 것보다 여러 사람이 보는 것이 버그를 발견하기 유용하다. 내가 생각하지 못했던 것도 누군가는 한 눈에 찾아낼 것이다. 특히나 하나의 fix 작업에 세개의 side effect를 발생시키는 개발자가 조직 내에 있다면.. 중요성은 더 말할 것도 없다.

#### 수준 상향 평등화

다른 사람의 코드를 읽을 기회가 많아지면서, 타인의 코드를 통해 배울 기회를 얻을 수 있다. 또한 코드리뷰를 통해 서로에게 더 좋은 방법을 제시하고 합리적인 방향을 찾아가며 이해 수준이 상향 평등화된다. 단순히 사전에 버그를 발견하거나 문제점을 찾는 목적을 넘어서 전체적인 조직의 역량을 강화할 수 있는 것이다.

#### 개발 표준 준수

정해진 표준 준수를 통해서 많은 사람들의 개발 결과물이 일관된 스타일을 유지할 수 있도록 도와준다. 또한 타인의 코드를 살펴보게 되면서, 같은 작업을 다른 사람이 두번씩 작업하는 일이 없어진다.

#### 책임감 강화

설령 배포된 코드에 문제가 발생했을 때, '누가 이 버그를 만들었는지'가 아니라 '왜 아무도 이 문제점을 발견하지 못했는지'에 대해 초점이 맞춰진다. 개발 결과물에 대한 책임이 그 코드를 만든 사람 개인이 아닌, 우리 모두에게 있다는 문화를 정착하는 것이다.

#### 인수인계 비용 감수

지속적인 코드리뷰는, 개중 어떤 한 사람이 조직을 떠나게 되었을 때 인수인계 비용을 크게 감소시켜준다. 사실 코드에 대해서는 인수인계를 할 필요도 없는 수준이 되게 되는 것이다.



## 🚀 올바른 코드리뷰의 방향

#### ✋ 능동적인 참여

사실 상 문화 정착에 있어 가장 중요한 것은 도구보다도 조직원들의 자발적인 참여다. 요청자도, 리뷰어도 모두 자발적인 마음에서 능동적인 태도로 임할 때 가장 빠르게 코드리뷰 문화가 정착될 수 있다.

#### 🛠 효율적인 도구

사실 오프라인으로만 이루여지는 코드리뷰 문화는 절대 고착 될 수 없다. 꾸준히 유지하기에 너무나 큰 비용이 드는 것이다. GitHub이나 GitLab 등의 온라인 리뷰 환경이 구성 될 수 있는 도구가 필요하다.

#### 📋 규칙과 강제성

능동성을 갖되, 환경의 강제성이 필요하다. 하고싶은 사람끼리 하고싶을 때만 하는 것이 아니라, 적어도 최소한의 강제성을 가지고 잘 정리된 환경 속에서 규칙을 준수해야하는 것이다.



# 🌱 코드리뷰 도입하기

## 👌 코드 승인제 수립하기

우선 조직 내에 합리적인 브랜치 전략이 구성되어 있어야 한다. 브랜치 전략을 세우는 방법에 대해서는 [git 브랜치 전략 세우기](../2003080157) 글을 참고 바란다.

내가 속한 조직의 경우에는 크게 master, develop 그리고 feature 브랜치를 가지고 있다. feature 브랜치는 기능 단위로 develop 브랜치에서 분기하여 개발 완료 후 다시 develop 브랜치에 병합한다. 이 때 **코드리뷰를 진행한 후, 리뷰가 승인 되어야만 develop 브랜치로 병합할 수 있도록 정의했다.**

자세한 flow를 살펴보자.

1. feature 브랜치에서 작업한 코드들을 commit&push한다.
2. feature > develop 브랜치로 merge하기 위한 pull-request를 생성한다.
3. 구성원들이 해당 pr을 리뷰한다. (Comment / Approve / Request changes)
4. 1개 이상의 approving이 되면 개발자 혹은 assignees가 해당 브랜치를 develop 브랜치로 병합한다.

아래는 해당 과정을 강제화하기 위한 github 설정법 및 리뷰 방법을 안내한다. 

### ⚙️ 'Code review assignment' 설정하기

1. GitHub organization 페이지에서 **Team** 탭을 클릭한다.

   <img src="https://help.github.com/assets/images/help/organizations/organization-teams-tab.png" style="zoom:67%;" />

2. Team 탭에서 팀의 이름을 클릭한다.

   ![](https://help.github.com/assets/images/help/teams/click-team-name.png)

3. 팀 페이지에서 **Settings** 버튼을 클릭한다.

   <img src="https://help.github.com/assets/images/help/teams/team-settings-button.png" style="zoom:80%;" />

4. 좌측 메뉴에서 **Code review assignment** 메뉴를 클릭한다.

   <img src="https://help.github.com/assets/images/help/teams/review-assignment-button.png" style="zoom:67%;" />

5. **Enable auto assignment**를 활성화한다.

   <img src="https://help.github.com/assets/images/help/teams/review-assignment-enable.png" style="zoom:67%;" />

6. "How many team members should be assignment to review?"에서 pull request 별 최소 승인자를 설정해준다.

   <img src="https://help.github.com/assets/images/help/teams/review-assignment-number.png" style="zoom:50%;" />

7. "Routing alagorithm"에서 원하는 알고리즘을 선택한다.

   *'Round robin'* 알고리즘은 가장 최근의 리뷰 요청을 받은 사람을 기준으로 리뷰어를 선택하며, 리뷰어에게 할당된 미해결 검토 수를 고려하지 않는다.

   *'Load balance'* 알고리즘은 각 멤버들 간 리뷰 요청 수를 기반으로 리뷰어를 선택한다. 각 멤버들의 미해결 검토수를 고려하여 모든 멤버들이 30일 동안 동일한 수의 pull request를 리뷰하도록 한다.

   <img src="https://help.github.com/assets/images/help/teams/review-assignment-algorithm.png" style="zoom:50%;" />

8. 그 외 기타 설정은 선택적으로 설정하며, **Save changes**를 클릭하여 설정을 완료한다.

### 🤏 리뷰하기

![](https://help.github.com/assets/images/help/pull_requests/review-header-with-line-comment.png)

pull request를 리뷰하는 데는 3가지 상태를 리뷰할 수 있다.

* **Comment**: 승인하거나 변경 요청 없이, 일반적인 피드백을 주는 것이다.
* **Approve**: 브랜치 병합을 허용한다. 보통 👍 표시와 함께 approve해준다. (따봉 표시를 받으면 기분이 좋다.)
* **Request changes**: 병합전에 해결해야 하는 사항을 피드백해준다.

![](https://help.github.com/assets/images/help/pull_requests/pull-request-review-statuses.png)

위 과정처럼 pull request에 대해 한번에 리뷰할 수 있지만, 수정사항 별로 개별 의견을 남길 수도 있다. 각 수정사항 별로 리뷰하고, 요약설명과 함께 approve하거나 request changes할 수 있다.

#### 수정사항 별로 피드백하기

Comment나 Approve는 피드백을 주는 것이 전부지만, Request changes는 파일/라인 별로 수정사항을 피드백 할 수 있다.

1. pull request의 **Files changed** 메뉴에 진입한다.

   ![Files Changed tab](https://help.github.com/assets/images/help/pull_requests/pull-request-tabs-changed-files.png)

2. 피드백하려는 코드 라인 위로 마우스를 가져간 다음 파란색 피드백 아이콘을 클릭한다. 여러 줄에 피드백을 추가하려면 클릭하여 드래그해서 범위를 선택한 다음 피드백 아이콘을 클릭하면 된다.

   ![Blue comment icon](https://help.github.com/assets/images/help/commits/hover-comment-icon.gif)

3. 피드백을 입력한다.

   ![Comment field](https://help.github.com/assets/images/help/pull_requests/comment-field.png)

4. 직접적으로 코드를 작성하여 제안하려면, 우측의 가장 첫번째 아이콘을 클릭하여 suggestion 블락 내에서 텍스트를 입력한다. (추후 pr 등록자는 해당 내용을 바로 적용할 수 있다.)

   ![Suggestion block](https://help.github.com/assets/images/help/pull_requests/suggestion-block.png)

5. 리뷰를 완료하면, **Start a review** 버튼을 클릭한다. 만약 이미 리뷰가 시작된 경우였다면 **Add review comment** 버튼을 클릭하면 된다.

   ![Start a review button](https://help.github.com/assets/images/help/pull_requests/start-a-review-button.png)

##### 확인한 파일 표시하기

리뷰를 완료한 파일에 대해서는 리뷰 완료를 표시할 수 있고, 해당 파일에 대해서는 수정사항 목록 내에서 접혀져 숨김처리 된다. 물론 다시 보고싶을 때는 언제든지 버튼을 체크해제할 수 있다.

![Viewed checkbox](https://help.github.com/assets/images/help/pull_requests/viewed-checkbox.png)

##### 리뷰 등록하기

모든 수정사항에 대해 리뷰를 완료했다면, 리뷰를 제출한다.

1. pull request의 **File changed** 메뉴에 진입한다.

   ![Files Changed tab](https://help.github.com/assets/images/help/pull_requests/pull-request-tabs-changed-files.png)

2. 상단의 **Review changes** 버튼을 클릭한다.

   ![Review changes button](https://help.github.com/assets/images/help/pull_requests/review-changes-button.png)

3. 요약 내용과 함께 리뷰를 등록한다.

   ![Review summary comment window](https://help.github.com/assets/images/help/pull_requests/review-summary-comment-window.png)

4. 원하는 뷰 타입을 선택 후 **Submit review** 버튼을 클릭하여 리뷰를 제출한다.

   ![Radio buttons with review options](https://help.github.com/assets/images/help/pull_requests/pull-request-review-statuses.png)

   

### 🤝 리뷰받기

#### 제안받은 내용 반영하기

만약 리뷰어들이 피드백한 내용들이 있다면, 이를 같이 코드에 반영할 수 있다.

1. 반영할 제안에 대해 **Commit suggestion** 버튼을 클릭한다.

   ![Commit suggestion button](https://help.github.com/assets/images/help/pull_requests/commit-suggestion-button.png)

2. 만약 여러개의 제안을 한번에 반영하고 싶다면 **Add suggestion to batch** 버튼을 클릭한다.

   ![Add suggestion to batch button](https://help.github.com/assets/images/help/pull_requests/add-suggestion-to-batch.png)

3. 간단하게 커밋 메시지를 작성하고 **Commit changes** 버튼을 클릭하여 커밋해준다.

   ![Commit message field](https://help.github.com/assets/images/help/pull_requests/suggested-change-commit-message-field.png)

#### 브랜치 병합하기

1. Approve 최소 인원이 충족되면, **Merge pull request** 버튼이 활성화된다. 드롭다운으로 다음을 수행할 수 있다.

   * **Merge pull request**: feature의 모든 커밋이 base 브랜치로 병합되어 커밋된다.

     <img src="https://help.github.com/assets/images/help/pull_requests/standard-merge-commit-diagram.png" alt="standard-merge-commit-diagram" style="zoom: 67%;" />

   * **Squach and merge**: feature의 모든 커밋을 하나의 커밋으로 병합한다.

     <img src="https://help.github.com/assets/images/help/pull_requests/commit-squashing-diagram.png" alt="commit-squashing-diagram" style="zoom:67%;" />

   * **Rebase and merge**: 모든 커밋이 merge 커밋 없이 개별적으로 base 브랜치에 추가된다.

2. 커밋메시지를 입력한 후 **Confirm merge** 버튼을 눌러 브랜치를 병합한다.

   ![Commit message field](https://help.github.com/assets/images/help/pull_requests/merge_box/pullrequest-commitmessage.png)

3. 필요에 따라 작업을 완료한 feature 브랜치를 삭제한다. (repo가 깔끔하게 유지된다.)



## 🤟 표준 가이드 수립하기

git에는 특정 상황에서 특정 스크립트를 실행할 수 있는 **Git Hooks**을 제공한다. 예를 들면 commit 전, push 전 등의 단계에서 스크립트를 실행 할 수 있도록 한다. 이 때 이 Git Hooks를 통해서 자동으로 코드 컨벤션을 체크하도록 할 수 있다. 코딩 스타일에 대해서는 사람이 리뷰하는 대신에 도구를 활용하면 많은 수고가 줄어든다.



# ✨ 리뷰를 대할 우리들의 자세

### 🙌 무엇을 리뷰하는가?

* 기능의 정상 동작 여부

* 버그의 발생 소지

* 가독성과 유지보수 편의성

* 개발 표준(code convention)의 준수 여부

* 기존 코드와의 중복성

### 🤏 리뷰를 할 때

어쨌든 다른 사람에게 의견을 준다는 것은 논쟁으로 이어질 수 있다. 최대한 둥글고 둥글게 진행되어야한다. 또한 피드백을 주려는 자세 외에도, 상대방의 좋은 점을 배우려는 자세가 필요하다. 후임에게서도, 주니어에게서도 나는 생각치 못했던 점들을 배울 수 있다. 단순히 리뷰를 해주는 것에 그치지 않고 내 것으로 만들기 위한 노력이 필요하다.

### 👌 리뷰를 받을 때

상처받지 않는 것이 가장 중요하다. 자신만만하게 짠 코드도 여러가지 요인에 의해 수정을 요청받을 수 있다. 잘못되어서가 아니라, 더 높은 품질의 코드를 작성하기 위한 긍정적인 방향이다. 결단코 코드와 나 자신을 동일시해서는 안 된다. 코드는 내가 아니라, 그저 내가 작성한 코드일 뿐이다.