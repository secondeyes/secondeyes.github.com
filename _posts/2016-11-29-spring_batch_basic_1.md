---
layout: post
title:  "spring batch 개념 정리"
description: "spring batch에서 사용하는 기본 개념에 대해서 알아봅니다."
author: codebasik
date:   2016-11-29 09:00:00 +0900
categories: spring
published: true
---

> spring batch의 기본 개념을 정리합니다.<br/>
  아래 내용은 [Spring Batch - Reference Documentation](http://docs.spring.io/spring-batch/trunk/reference/html/index.html) 을 참고하였습니다.  

## spirng batch 주요 개념

![spring_batch]({{ site.url }}/img/2016-11-29/spring_batch_basic_1.png){: style="width:70%; display:block;margin:30px auto 0;"}

### Job
> spring batch에서 Job은 step들을 위한 컨테이너에 불과합니다.
 
아래 그림을 먼저 살펴보겠습니다. 단순히 그림에 나타난 내용으로만 작업이 의미하는 것이 무엇인지 유추해보겠습니다.<br/>
( 저는 아직 Job , JobInstance , JobExecution이 무엇을 의미하는지 알지 못합니다.)

![Job]({{ site.url }}/img/2016-11-29/spring_batch_job_1.png){: style="width:70%; display:block;margin:30px auto 0;"}
 
 * <b>Job</b> : 'EndOfDay Job'이라는 이름의 작업.<br/>
 * <b>JobInstance</b> : 2007년 5월5일을 위한 Job ('EndOfDay Job') 
 * <b>JobExecution</b> : JobInstance (2007년 5월5일을 위한 'EndOfDay Job')의 첫 번째 시도
 
위 내용을 풀어보면<br/>
'EndOfDay Job'라는 작업 (Job)이 있고, 매일 실행된다는 가정아래에 그중 어느날 (2007년 5월5일)을 위한 작업(JobInstance)이 있습니다.<br/>
그리고 2007년 5월5일에 처음 수행된 작업 (JobExecution)이 있습니다.<br/>
아마도.. Job은 날짜별로 JobInstance를 가질 수 있고, JobInstance는 한 날짜에 여러번 실행이 될 수 있는 것 같습니다.<br/>
(여기서 계속 언급된 날짜는 하나의 파라메터에 불과합니다. 관련 내용은 아래에서 다시 언급합니다.) <br/>
조금 더 자세히 알아보겠습니다.

### Job Instance

 * Job Instance는 논리적 작업 실행의 개념을 나타냅니다. 
 * JobInstance = Job + identifying JobParameters
  
### Job Execution

 * JobExecution은 Job을 실행하려는 단일 시도의 기술적 개념을 나타냅니다
 * 작업이 실행될 때 실제 수행된 Execution을 정의합니다.<br/> 2007년 5월5일 첫번째 작업이 실패하여 두번째 작업이 수행한 경우, 각각의 Job Execution은 다른것으로 분류되고 같은 Job Instance를 수행한 것으로 볼 수 있습니다.
   
### Job Parameter

 * Job Parameter는 배치 작업을 시작하는데 사용되는 매개변수의 집합입니다.<br/>
   식별을 위해 사용되거나 실행 중에 참조 데이터로 사용할 수도 있습니다.
 * JobInstance를 구별할 수 있는 기준

![JobParameters]({{ site.url }}/img/2016-11-29/spring_batch_job_2.png){: style="width:70%; display:block;margin:30px auto 0;"}  

### Step
> Step은 Job 내부에 구성되어 실제 배치작업 수행을 위해 작업을 정의하고 제어합니다.<br/> 
  즉, Step에서는 입력 자원을 설정하고 어떤 방법으로 어떤 과정을 통해 처리할지 그리고 어떻게 출력 자원을 만들 것인지에 대한 모든 설정을 포함합니다.
  
Step은 Job의 독립적이고 순차적 단계를 캡슐화하는 도메인 객체입니다. 그러므로 모든 Job은 적어도 하나 이상의 Step으로 구성되며 Step에는 실제 배치작업을 처리하고 제어하기 위해 필요한 모든 정보가 포함됩니다. 

![StepExecution]({{ site.url }}/img/2016-11-29/spring_batch_step_1.png){: style="width:70%; display:block;margin:30px auto 0;"}

### Step 유형
#### Chunk-Oriented Processing
Chunk 기반 처리(이하 Chunk)는 스프링 배치에서 가장 일반적으로 사용하는 Step 유형입니다.<br/>
Chunk는 data 한번에 하나씩 읽어 트랜잭션의 범위 안에서 로직을 처리한 후 한번에 쓰는 방식으로 구성되어 있습니다.<br/>
ItemReader -> ItemProcessor -> ItemWriter 단계로 진행됩니다.<br/>
아래와 같이 파일에 기입된 알파벳 소문자를 대문자로 변환하여 다시 파일에 쓰는 작업을 진행하는 예를 들어보겠습니다.
```
a
b
c
d
e
```
chunk 방식으로 처리하는 과정을 보면,<br/>

 * ItemReader : 파일에서 알파벳 5개를 한번에 읽어옵니다.
 * ItemProccessor : 5개의 알파벳을 한개씩 대문자로 변환합니다.
 * ItemWriter : 변환된 5개의 대문자 알파벳을 파일에 씁니다.
 
 여기서 주의할 것은!!<br/>
 reader/writer의 경우 한번에 데이터를 읽고/쓰지만 Itemprocessor 단계에서는 알파벳 5개중 한개씩 순차적으로 변환 처리를 한다는 것입니다.<br/>
 chunksize를 지정할 수 있습니다. 만약 chunksize를 2로 지정한 경우, 위의 예제에서 ItemReader는 처음에 2개의 알파벳을 읽어 대문자로 변환 후 저장하는 단계를 수행하게 됩니다. 

#### TaskletStep
Tasklet은 Step의 또다른 유형중 하나입니다. Tasklet은 한방!으로 해결하고 싶을 때 사용합니다.<br/>
예를들어 작업이 단순히 프로시저를 호출하는 작업 한개로 이루어졌다면 이런 경우 Tasklet을 사용합니다.

### StepExecution
Step에도 JobExecution에 대응되는 StepExecution이 있습니다.
StepExecution은 Step을 수행하기 위한 단 한번의 Step 시도를 의미하며 시도될 때 마다 매번 생성이 됩니다.
StepExecution에서는 Step 실행 도중 어떤 일이 발생했는 가에 대한 속성을 저장하는 역할을 합니다. 에러가 발생했는지, Step이 정상 완료됐는지 확인이 가능합니다.
