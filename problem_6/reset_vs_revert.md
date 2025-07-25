# Git Reset vs. Revert: 되돌리기의 두 가지 접근법

Git에서 특정 커밋의 변경 사항을 취소하는 데는 크게 `reset`과 `revert`라는 두 가지 명령어가 사용됩니다. 두 명령어는 동일한 목표를 가질 수 있지만, **과거의 기록을 다루는 방식**에서 근본적인 차이가 있으며, 이는 협업 환경에서 매우 중요한 의미를 가집니다.

---

## **1. `git reset`: 과거를 지우는 시간 여행**

`reset`은 현재 브랜치가 가리키는 포인터(`HEAD`)를 과거의 특정 커밋으로 강제로 이동시키는, 파괴적인 방식의 되돌리기입니다. 마치 "이 커밋 이후의 일은 전부 없었던 걸로 하자"라고 선언하는 것과 같습니다.

* **동작 방식**: 커밋 히스토리 자체를 **재작성(Rewrite)** 합니다. 지정한 커밋 이후의 모든 커밋 기록이 사라집니다.
* **주요 옵션**:
    * `--soft`: 커밋 기록만 지우고, 변경했던 파일 내용은 스테이징 영역(Staged)에 그대로 남겨둡니다.
    * `--mixed` (기본값): 커밋 기록과 스테이징 영역을 지우고, 변경했던 파일 내용은 작업 디렉토리(Unstaged)에 남겨둡니다.
    * `--hard`: 커밋 기록, 스테이징 영역, 작업 디렉토리의 변경 내용까지 **모두 영구적으로 삭제**합니다. 가장 강력하고 위험한 옵션입니다.
* **핵심**: **과거의 기록을 지워버립니다.**

---

## **2. `git revert`: 과거를 인정하고 바로잡는 새로운 기록**

`revert`는 과거의 잘못을 없었던 일로 만드는 것이 아니라, "과거의 특정 커밋에서 했던 변경 사항을 취소하는 새로운 커밋을 만들자"라고 선언하는, 비파괴적인 방식의 되돌리기입니다.

* **동작 방식**: 지정한 커밋이 했던 변경 사항과 정확히 반대되는 작업을 수행하는 **새로운 커밋(New Commit)**을 생성합니다.
* **예시**: 만약 특정 커밋이 파일에 A라는 내용을 추가했다면, `revert`는 A라는 내용을 삭제하는 새로운 커밋을 만듭니다.
* **핵심**: **과거의 기록을 그대로 보존하면서, 새로운 기록을 추가하여 문제를 해결합니다.**

---

## **협업 시 `revert`를 추천하는 이유: 공유된 역사는 신성하다**

결론적으로, **여러 사람과 공유하는 브랜치(예: `main`, `develop`)에서는 절대 `reset`을 사용해서는 안 되며, 반드시 `revert`를 사용해야 합니다.**

그 이유는 **'히스토리 충돌'** 때문입니다.

만약 팀원 A가 공유 브랜치에 `reset`을 사용하여 특정 커밋들을 지워버리고 원격 저장소에 강제로 `push`했다고 가정해 봅시다. 이미 그 커밋들을 자신의 로컬 저장소에 받아갔던(pull) 다른 팀원 B, C의 컴퓨터에는 지워진 커밋 기록이 그대로 남아있습니다.

이 상태에서 B나 C가 새로운 작업을 하고 `push`를 시도하면, 그들의 로컬 히스토리와 원격 저장소의 히스토리가 서로 달라(Diverged) 엄청난 충돌이 발생합니다. 이 문제를 해결하는 것은 매우 복잡하고, 최악의 경우 다른 사람의 작업 내용을 덮어쓸 위험도 있습니다.

반면, `revert`를 사용하면 "이 변경 사항을 취소했습니다"라는 내용의 **새로운 커밋**이 만들어질 뿐입니다. 다른 팀원들은 평소처럼 `git pull`을 통해 이 새로운 커밋을 받아오기만 하면 됩니다. 프로젝트의 전체 역사는 깨끗하게 한 줄로 이어지며, 누가 무엇을 되돌렸는지 명확하게 기록으로 남습니다.

**요약**: `reset`은 아직 나 혼자만 보고 있는 개인 브랜치에서 지저분한 커밋들을 정리할 때만 사용하고, `revert`는 팀원 모두가 공유하는 공공의 역사에 변경을 가할 때 사용하는 안전하고 투명한 방법입니다.