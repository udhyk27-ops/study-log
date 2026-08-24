---
notion_page_id: 3bc81856-910c-815d-b03d-eeb427e4eed5
---
SQL*Loader로 CSV 데이터를 적재하면서 겪은 문제와 해결

최근 업무에서 CSV 데이터를 Oracle 테이블에 대량으로 적재하기 위해 SQL*Loader를 사용했다.

처음에는 간단한 .ctl 파일을 작성하고 실행하면 될 것이라 생각했지만, 실제 환경에서는 몇 가지 문제가 발생했다.

파일을 찾지 못하는 문제

SQL*Loader 실행 과정에서 SQL*Loader-500 파일 관련 오류가 발생했다.

확인해보니 데이터 파일 경로에 한글이 포함되어 있었고, 환경에 따라 해당 경로를 정상적으로 인식하지 못하는 문제가 있었다.

그래서 SQL*Loader에서 사용하는 파일 경로를 영문 경로로 변경해 문제를 해결했다.

CSV 데이터가 정상적으로 들어가지 않는 문제

CSV 파일의 구분자와 인코딩도 확인해야 했다.

최종적으로 다음과 같이 FIELDS TERMINATED BY와 OPTIONALLY ENCLOSED BY를 설정하고, 빈 컬럼 처리를 위해 TRAILING NULLCOLS를 사용했다.

FIELDS TERMINATED BY ','
OPTIONALLY ENCLOSED BY '"'
TRAILING NULLCOLS

또한 CSV 파일에 UTF-8 BOM이 포함되어 있는 경우 첫 번째 컬럼 값에 예상하지 못한 문자가 들어갈 수 있어 파일 인코딩도 함께 확인했다.

마무리

이번 작업을 통해 SQL*Loader는 단순히 CSV를 테이블에 넣는 도구가 아니라 파일 경로, 인코딩, 구분자, NULL 처리 등을 함께 확인해야 하는 도구라는 것을 알게 됐다.

특히 대량 데이터를 적재할 때는 오류 메시지만 보기보다 실제 입력 파일과 .ctl 설정을 같이 확인하는 것이 문제 해결에 효과적이었다.
