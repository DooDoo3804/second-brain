---
title: Spring Boot Batch
tags:
  - 스프링
---
## Spring Boot Batch

'대량의 데이터를 처리하는 작업을 자동화' 하는 프로그램을 말한다. (예를 들면, 유저 데이터 일괄 갱신 등) 보통 스케줄러와 같이 사용하며, 특정 시간에 프로그램이 동작하도록 설계한다. 파이썬의 경우 [[APScheduler]]로 구현할 수 있다. Spring Boot Batch는 배치 프로그램을 사용하여 시스템의 부하를 줄이고 효율적인 데이터 처리를 가능하게 하는 프레임워크를 의미한다.

>[!question] 배치 프로그램과 스케줄러는 동일한 개념인가?
>**배치프로그램**은 일괄 처리를 위한 프로그램이며 '정해진 시간에 실행되지 않고 실행 명령이 있는 경우 실행'한다. 즉, 대량의 데이터를 일괄적으로 처리하기만 한다.
>**스케줄러**는 '정해진 시간에 자동으로 실행'하는 프로그램이다. 사용자의 특정한 행동, 특정 조건, 정해진 시간, 주기에 따라서 실행되도록 설정할 수 있다.

배치 프로그램은 대량 데이터 처리(대규모 DB, 로그 파일), 자동화된 작업 처리, 분산 처리, 재시도 및 로깅, 데이터 분석(데이터 마이그레이션, 백업 및 복원, 분석)을 할 때 사용된다. 

## Spring Batch 용어

### Job

전체 배치 처리 과정을 추상화한 개념이다. 하나 또는 그 이상의 Step을 포함하여 스프링 배치 계층에서 가장 상위에 위치한다. 각 Job은 고유한 이름을 가지며, 실행에 필요한 파라미터와 함께 JobInstance를 구별하는데 사용된다. Step와 순서를 정의하고, Job의 재사용 가능성을 정의한다.

### JobInstance

특정 Job의 실제 실행 인스턴스를 의미한다. 한 번 생성된 JobInstance는 데이터를 처리되는 데 사용되며, 실패했을 경우 같은 JobInstance를 다시 실행하여 작업을 완료할 수 있다. 여러 JobInstance가 생성되더라도 독립적으로 실행과 실패가 존재한다. JobInstance는 JobParameter를 이용하여 구분한다.

### JobParameters

JobInstance를 생성하고 구별하는 데 사용되는 파라미터이다. Job이 실행될 때 필요한 파라미터를 공유하며 JobInstance를 구별하는 역할도 한다. 스프링 배치는 `String`, `Double`, `Long`, `Date` 4가지 타입의 파라미터를 지원한다.

### JobExecution

JobInstance의 실행에 대한 실행 상태, 실행 시간, 종료 시간, 생성 시간 등 세부 정보를 저장하는 인스턴스다. 실행한 JobInstance에 대한 새로운 JobExecution이 생성된다.

### JobRepository

배치 작업에 관련된 모든 메타 정보를 저장하고 관리하는 메커니즘이다. Job과 Step을 구현하기 위한 CRUD 작업을 제공한다. JobInstance, JobExecution, StepExecution, JobParameters 등의 정보를 포함하고 있다. Job이 실행될 때, 새로운 JobExecution과 StepExecution을 생성하고, 이들을 통해 실행 상태를 추적한다.

### JobLauncher

Job을 실행하는 인터페이스이다. JobRepository를 통해 실행 상태를 유지하며, Job과 JobParameters를 받아 실행한다. 

### Step

Job의 하위 단계로 실제 배치 처리 작업이 이뤄지는 최소 단위이다. 배치 작업의 독립적이고 순차적인 단계(Job)를 캡슐화하는 도메인 객체이다. 한 개 이상의 Step으로 Job이 구성되며, 이들은 순차적으로 처리된다. Step은 Tasklet 또는 Chunk 방식으로 구성된다. 

### StepExecution

Step의 한 번의 실행을 나타내며, Step의 실행 상태, 실행 시간 등의 정보를 포함한다. 각 Step의 실행 시도마다 새로운 StepExecution이 생성된다. 읽은 아이템의 수(ReadCount), 쓴 아이템 수(WriteCount), 커밋 횟수(CommitCount) 등 상세 정보도 포함한다.

### ExecutionContext

Step 간 또는 Job 실행 도중 데이터를 공유하는 데 사용되는 저장소이다. JobExecutionContext와 StepExecutionContext가 존재하며, 범위와 저장 시점에 따라 적절하게 사용된다. Job이나 Step이 실패했을 경우, ExecutionContext를 통해 마지막 실행 상태를 재구성하여 재시도 또는 복구 작업을 수행할 수 있다.

## Spring Boot Batch의 종류

스프링 배치는 Task를 처리하는 방법에 따라 Tasklet과 Chunk 방식으로 나뉜다.

### Tasklet

배치의 Step 단계에서 '단일한 레코드(row)'나 '파일'을 하나의 작업만 처리하는 방식을 말한다. 그렇기에 간단한 단일 작업을 수행할 때 사용된다. 하나의 트랜잭션에서 하나의 처리를 진행한다. 일반적으로 파일을 읽고 처리하고 DB에 쓰는 작업을 수행한다. 단일 처리 작업이기 때문에 하나의 작업이 끝날 때 까지 대기해야 한다. 대용량 데이터 처리에 부적합하다. Tasklet의 execute 메서드는 Step의 모든 처리가 끝날 때까지 계속 호출된다.

### Chunk

배치의 Step 단계에서 '단일한 레코드(row)를 묶어서' 여러 작업을 처리하는 방식을 말한다. '묶인 레코드를 하나의 트랜잭션으로 처리'하며 실패하는 경우 롤백을 수행한다. 대용량 데이터를 처리할 때, 성능이 향상되고 중복 레코드 처리나 실패한 레코드 처리 등 예외 상황에 대한 대처가 용이하다. DB에서 데이터를 조회하여 처리하는 작업의 경우에 사용된다.

>[!info] Chunk
>데이터를 일정한 크기로 나눈 데이터 셋을 의미한다. Chunk 단위로 나누면 전체 데이터를 한 번에 처리하지 않기에 메모리 부하를 줄이고 성능을 향상시킬 수 있다.

Chunk 방식은 'Reader-Processor-Writer' 방식을 사용한다. 이러한 처리 방식은 대용량 처리를 위해서 사용하며 'Reader'는 데이터를 읽어 들이는 역할, 'Processor'는 읽어 들인 데이터를 가공하거나 필터링하는 역할, 'Writer'는 가공된 데이터를 저장하는 역할을 수행한다.

### Paraller Chunk

Chunk 방식의 처리에서 빠른 처리 속도를 위해 Chunk를 독립적으로 처리하여 여러 개의 Chunk를 병렬로 처리하는 방식이다. 여러 서버에서 동시에 작업을 처리할 때 사용한다.

## Spring Boot Batch 구현

>[!danger]
>코드 미완

## Scheduler

DJango의 APScheduler는 스케줄러를 지원해 주지만, Spring Batch는 스켈줄러가 아닌 배치 프로그램만 지원한다. 그렇기에 Quartz, Jenkins와 같은 전용 스케줄러를 사용하여 Job을 실행해야 한다. Spring은 Quartz Scheduler를 지원하므로, [[Quartz Scheduler]]를 사용하여 Batch Job을 실행하는 것도 방법이다.



