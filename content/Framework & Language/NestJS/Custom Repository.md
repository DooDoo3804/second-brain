---
title: Custom Repository
tags:
  - nestjs
---
## Custom Repository

TypeOrm은 사용자가 레포지토리를 수정하여 적용할 수 있도록 지원해준다. TypeOrm의 레포지토리도 일반적인 메소드를 제공하고 있지만, 더 복잡한 쿼리를 작성하기 위해서는 레포지토리를 직접 수정/작성 해야 한다. 일반적인 [[Repository]]에 관한 설명은 해당 페이지를 참고하면 된다.


>[!note]
>TypeOrm 0.2버전에서는 Entity repository로 user custom repository를 구현했지만, 0.3버전으로 업데이트되면서 Entity Repository가 삭제되었다. 하지만, 여전히 구현이 가능하지만, 구현이  좀 복잡해졌다. (typeorm에서 repository사용을 지양하는 느낌...)

## Custom Repository 구현

TypeOrm 버전이 0.3으로 업데이트가 된 이후로 구현은 다음의 과정을 거친다.