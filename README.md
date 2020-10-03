# Lab5 Team Activity - Git workflow

팀원 권우석, 박지윤, 우태희, 오승기, 빈현우, 정재준  

---

### 개요  

Vincent Driessen이 2010년 1월 5일 개발 모델에 대하여 적은 글은 수많은 개발자들이  
자신의 프로젝트를 진행하는 데 영향을 끼쳤고, 아직까지도 가장 많이 이용되는  
workflow중 하나이다.  
  
2020년 5월 Vincent Driessen는 자신의 개발 모델에 대한 성찰에서 "모든 프로젝트에  
적용되는 만병통치약은 없으니 자신의 프로젝트 맥락에 맞춰 사용해야 한다."라는 말을 남겼다.  
  
이처럼 10년이 지난 오늘날에도 사랑받고 있는 workflow이기에 원문을 번역하며  
개발 모델을 학습해보기로 했다. 원문은 Git의 장점과 함께 자신의 개발 모델을  
branch를 중심으로 설명한다. branch는 Main branch(master, develop)와  
Supporting branch(Feature, Release, Hotfix)로 나뉘어 총 5개이고 각  
branch의 목적과 사용 방법을 설명하는 방식으로 글이 전개된다.  

---

이 게시물에서는 작년 몇몇 프로젝트(일과 개인적인 프로젝트 모두)에서 소개한 성공적인  
개발 모델을 보여주려 한다. 나는 한동안 이 개발 모델에 대해 글을 쓰고 싶었지만 지금까지  
시간이 허락하지 않았다. 나는 프로젝트의 세부 사항은 생략하고, 단지 branching 전략과   
형상 release 관리에 대해서만 설명할 것이다.

### Why Git?

중앙 집중형 관리 시스템 와 비교했을 때 Git이 가지는 장단점에 대한 치열한 논쟁은 웹을 통해  
확인할 수 있다. 그곳에는 불꽃 튀는 전쟁이 계속해서 일어나고 있다. 개발자로서, 나는 오늘날  
어떤 도구보다 Git을 선호한다. Git은 merging과 branching에 대해 개발자가 생각하는 방식을  
완전히 바꾸었다. 내가 이전에 사용했던 고전적인 CVS/Subversion에서, merging과 branching은  
항상 위험한 것으로 여겨졌고(merge 충돌이 일어나면, 팀원들은 당신을 물어뜯을 것이다!)  
모든 것을 한 번에 진행해야 했었다.  
  
  
> CVS - Concurrent Versions System의 약자로 1980년대 만들어진 형상관리 프로그램이다. 파일 관리나 커밋 중 오류 시 롤백이 되지 않는 등 기술적인 문제가 많아 요즘은 사용되지 않는다.  
  
  
> Subversion - 문제가 있던 CVS를 대체하기 위해 탄생한 형상관리 도구이다. tag와 branchs라는 개념을 지원하여 형상(버전) 관리를 편하게 만들었다.

  


하지만 Git에서는, 이러한 작업들이 굉장히 쉽게 이루어지고 일별 workflow에서 핵심적인 요소 중 하나이다. 예를 들어, CVS/Subversion 교본에서는 branching과 merging은 전문가를 위한 마지막 챕터에서 다루는 주제이지만 Git 교본에서는 3번째 챕터에서 다루는 기본적인 주제이다.

Git의 단순함과 반복적인 특성의 결과로 branching과 merging은 더 이상 두려움의 대상이 아니다. 형상 관리 도구들은 그 무엇보다 branching과 merging을 도울 수 있어야 한다.


도구에 대한 이야기는 충분하니 개발 모델에 대해 이야기해보자. 앞으로 소개할 모델은 기본적으로 모든 팀원이 소프트웨어 개발 과정을 관리하기 위해 따라야 하는 절차에 지나지 않는다.


---

### The main branches

<img src= "images/main-branches.png" title = "main branch photo added"> </img>
> master

> develop

핵심적으로, 개발 모델은 기존에 있던 모델에서 영감을 받았다. 중앙 저장소는 제거되지 않는 두 개의 main branch(master와 develop)를 가지고 있다.

origin 저장소에 있는 master 브랜치는 모든 Git 사용자들에게 익숙할 것이다. master 브랜치와 평행하는 또 다른 브랜치는 develop이라 부른다.

우리는 origin/master를 HEAD의 소스코드가 항상 생산 준비 상태로 반영된 main branch로 여긴다.

우리는 origin/develop를 HEAD 소스코드가 항상 다음 배포를 위한 최신 개발 변화를 전달 준비가 된 main branch로 간주한다. 몇몇은 develop 브랜치를 '통합 branch'라 부르기도 한다. 이곳은 모든 automatic nightly builds가 건설되는 곳이다.

develop branch가 안정적인 위치까지 도달하여 배포할 준비가 되었을 때, 모든 변화는 master 브랜치에 병합되어야 하고 배포 번호로 tag 되어야 한다. 어떻게 이 과정이 완성되는지 자세한 설명은 차후에 논의하겠다.

그러므로, 변화가 master 브랜치로 병합되는 매 순간 정의에 의해 새로운 새로운 배포가 생산되는 것이다. 우리는 이 문제에 대해 매우 엄격하기 때문에 이론적으로는 master에 대한 commit이 있을 때마다 소프트웨어를 자동으로 구축하고 생산 서버에 롤아웃하기 위해 Git hook 스크립트를 사용할 수 있다.


