---
layout: post
title:  "Spring Batch 란?"
excerpt: ""

categories:
  - Spring-boot, Spring
tags:
  - [Spring-boot, Spring]

toc: true
toc_sticky: true
comments: true
 
date: 2021-09-18
last_modified_at: 2021-09-18
---
## 기본 지식: batch 프로그램이란?

배치 프로그램이란 간단히 말하면 '정해놓은 시간에 일괄적으로 무언가를 처리하는 프로그램' 이라고 할 수 있다.

좀 더 자세히 알아보기 위해 사전에 정의된 batch 프로그램의 정의를 알아보면 다음과 같다.

> ***일괄 처리**(batch processing)란 [컴퓨터 프로그램](https://ko.wikipedia.org/wiki/컴퓨터_프로그램) 흐름에 따라 순차적으로 자료를 처리하는 방식을 뜻한다. 초기의 일괄처리 방식은 사용자와 상호작용하는 것이 불가능했지만, 운영 체제가 발전함에 따라 [프로그램 입출력](https://ko.wikipedia.org/wiki/프로그램_입출력)을 통해 상호작용하는 것이 가능해졌다. 일괄 처리는 1950년대 전자 컴퓨팅 초기 시절 이후 [메인프레임 컴퓨터](https://ko.wikipedia.org/wiki/메인프레임_컴퓨터)와 함께하고 있다.*

음.. 뭔가 복잡해 보이지만 중요한 키워드는 ``일괄 처리`` 라고 보면 된다.

어떤 요청이 있을 때마다 건 바이 건으로 처리하는 것이 아닌, 할 일들이 어느정도 모이면 일괄적으로 처리하는 것이라고 할 수 있다.

그리고 일괄 처리를 위해서는 자연스럽게 특정 시간대에 실행되는 것이 보통이다. (할 일이 모여야 하니까)



왜 일괄 처리가 필요하냐고 물어보면, 성능 때문이라고 말할 수 있다.

비유를 하자면 우유 100개를 학교로 배달해야하는데, 1개씩 배달하는 것보다 100개를 한번에 모아서 배달하는게 시간이나 자원을 덜 쓰는 것과 같다.

수많은 데이터를 처리해야할 때 현실적으로 실시간 처리가 힘든 경우, batch 프로그램으로 특정 시간마다 일괄적으로 처리하는 것이다.



> batch 프로그램은 다음과 같은 조건을 만족해야 한다.
>
> - 대용량 데이터 - 배치 어플리케이션은 대량의 데이터를 가져오거나, 전달하거나, 계산하는 등의 처리를 할 수 있어야 합니다.
> - 자동화 - 배치 어플리케이션은 심각한 문제 해결을 제외하고는 **사용자 개입 없이 실행**되어야 합니다.
> - 견고성 - 배치 어플리케이션은 잘못된 데이터를 충돌/중단 없이 처리할 수 있어야 합니다.
> - 신뢰성 - 배치 어플리케이션은 무엇이 잘못되었는지를 추적할 수 있어야 합니다. (로깅, 알림)
> - 성능 - 배치 어플리케이션은 **지정한 시간 안에 처리를 완료**하거나 동시에 실행되는 **다른 어플리케이션을 방해하지 않도록 수행**되어야합니다.



## Spring Batch

이제 batch 프로그램이 무엇을 말하는지 알게 되었다.

그리고 Spring Batch는 말 그대로 Spring 진영에서 지원하는 batch 프로그램이라고 할 수 있다.

우리는 Spring MVC를 통해 MVC의 틀을 구현하는데 에너지를 쓰지 않고 비즈니스 로직에 집중해서 개발할 수 있게 되었다.

마찬가지로 Spring Batch를 사용한다면 일괄 처리해야하는 비즈니스 로직에만 집중할 수 있게 될 것이다.



## Spring Batch 기본 Flow 이해하기

배치의 일반적인 시나리오는 read -> processing -> write 로 이루어진다.

1. Read : 데이터 저장소에서 필요한 데이터를 읽는다.
2. Processing : 읽은 데이터를 필요한 방식에 따라 가공한다.
3. Write : 가공한 데이터를 다시 데이터 저장소에 저장한다.

다음 그림은 스프링에서 이러한 배치 처리를 어떻게 구현 했는지 배치 처리와 관련된 객체의 관계를 보여준다.

![img](https://github.com/cheese10yun/TIL/raw/master/assets/batch-obejct-relrationship.png)

- Job은 여러 단계(Step)을 가질 수 있다 (1:M)
- Step은 1개의 ItemReader, ItemProcessor, ItemWriter를 갖는다.
- 즉 Step은 위에서 말한 기본적은 Flow (Read -> Processing -> Write)로 흘러가고, Job은 여러 개의 Step으로 이루어져있다.



### 출처

- <https://jojoldu.tistory.com/324>
- <https://limkydev.tistory.com/140>



----------------------------------------------------------------------------------------------------------------------------------------

아래는 Spring Batch의 기본적인 구성요소에 대한 설명이다. 

<https://cheese10yun.github.io/spring-batch-basic/> 의 내용을 그대로 적었으며 (너무 정리가 잘 되어있어서..) 

해당 포스트 예제 따라하며 batch 사용법 익히는 중이다.



## Spring Batch 구성요소

### Job

- Job은 배치 처리 과정을 하나의 단위로 만들어 포현한 객체입니다. 또한 전체 배치 처리에 있어 항상 최상단 계층에 있습니다.
- 위에서 하나의 Job(일감) 안에는 여러 Step(단계)이 있다고 설명했던 바와 같이 **스프링 배치에서 Job 객체는 여러 Step 인스턴스를 포함하는 컨테이너 입니다**
- Job 객체를 만드는 빌더는 여러 개 있습니다. 여러 빌더를 통합하여 처리하는 공장인 JobBuilderFactory로 원하는 Job을 쉽게 만들수 있습니다.

```java
public class JobBuilderFactory {
    private JobRepostiroy jobrepository;

    public JobBuilderFactory(JobRepository jobRepository){
        this.jobrepository = jobrepository;
    }

    public JobBuilder get(String name){
        JobBuilder builder = new JobBuilder(name).repository(jobrepository);
        return builder;
    }
}
```

- JobBuilderFactory는 JobBuilder를 생성할 수 있는 get() 메서드를 포함하고 있습니다. get()메서드는 새로운 JobBuilder를 생성해서 반환하는 것을 확인할 수있습니다.
- JobBuilderFactory에서 생성되는 모든 JobBulder가 레포지토리를 사용합니다.
- JobBuilderFactory는 JobBuilder를 생성하는 역할만 수행합니다. 이렇게 생성된 JobBuilder를 이용해서 Job을 생성해야 하는데, 그렇다면 JobBuilder의 역할은 무엇인지 JobBuilder의 메서드를 통해 기능을 알아보겠습니다.

```java
public SimpleJobBuilder start(Step step){
    //(1)
    // Step을 추가해서 가장 기본이되는 SimpleJobBuilder를 생성합니다.
    return new SimpleJobBuilder(tihs).start(step);
}

public JobFlowBuilder start(Flow flow){
    //(2)
    // Flow를 실행할 JobFlowBuilder를 생성합니다.
    return new JobFlowBuilder(tihs).start(flow);
}

public JobFlowBuilder flow(Step step){
    //(3)
    // Step을 실행할 FlowJobBuilder를 생성합니다.
    return new JobFlowBuilder(tihs).start(step);
}
```

- JobBuilder는 직접적으로 Job을 생성하는 것이 아니라 별도의 구체적 빌더를 생성하여 변환하여 경우에 따라 Job 생성 방법이 모두 다를 수 있는 점을 유연하게 처리할 수 있습니다.

### JobInstance

- **JobInstance는 배치 처리에서 Job이 실행될 때 하나의 Job 실행 단위입니다.** 만약 하루에 한 번 씩 배치의 Job이 실행된다면 어제와 오늘 실행 각각 Job을 JobInstance라고 부를 수 있습니다.
- 각각의 JobInstance는 하나의 JobExecution을 갖는 것은아닙니다. 오늘 Job이 실행 했는데 실패했다면 다음날 동일한 JobInstance를 가지고 또 실행합니다.
- Job 실행이 실패하면 JobInstance가 끝난것으로 간주하지 않기 때문입니다. 그렇다면 JobInstance는 어제 실패한 JobExecution과 오늘의 성공한 JobExecution 두 개를 가지게 됩니다. **즉 JobExecution 는 여러 개 가질 수 있습니다.**

### JobExecution

- JobExecution은 JobIstance에 대한 한 번의 실행을 나타내는 객체입니다.
- 만약 오늘 Job이 실패해 내일 다시 동일한 Job을 실행하면 오늘/내일의 실행 모두 같은 JobInstance를 사용합니다.
- 실제로 JobExecution 인터페이스를 보면 Job 실행에 대한 정보를 담고 있는 도메인 객체가 있습니다. *JobExecution은 JobInstance, 배치 실행 상태, 시작 시간, 끝난 시간, 실패했을 때 메시지 등의 정보를 담고 있습니다. JobExecution 객체 안에 어떤 실행 정보를 포함 하고 있습니다.*

### JobParameters

- JobParameters는 Job이 실행될 때 필요한 파라미터들은 Map 타입으로 지정하는 객체 입니다.
- JobParameters는 JobInstance를 구분하는 기준이 되기도 합니다.
- JobParameters와 JobInstance는 1:1 관계입니다.

### Step

- Step은 실직적인 배치 처리를 정의하고 제어 하는데 필요한 모든 정보가 있는 도메인 객체입니다. Job을 처리하는 실질적인 단위로 쓰입니다.
- 모든 Job에는 1개 이상의 Step이 있어야 합니다.

#### StepExecution

- Job에 JobExecution Job실행 정보가 있다면 Step에는 StepExecution이라는 Step 실행 정보를 담는 객체가 있습니다.

### JobRepository

- JobRepository는 배치 처리 정보를 담고 있는 매커니즘입니다. 어떤 Job이 실행되었으면 몇 번 실행되었고 언제 끝났는지 등 배치 처리에 대한 메타데이터를 저장합니다.
- 예를들어 Job 하나가 실행되면 JobRepository에서는 배치 실행에 관련된 정보를 담고 있는 도메인 JobExecution을 생성합니다.
- JobRepository는 Step의 실행 정보를 담고 있는 StepExecution도 저장소에 저장하여 전체 메타데이터를 저장/관리하는 역할을 수행합니다.

### JobLauncher

- JobLauncher는 Job. JobParamerters와 함께 배치를 실행하는 인터페이스입니다.

### ItemReader

- ItemReader는 Step의 대상이 되는 배치 데이터를 읽어오는 인터페이스입니다. File, Xml Db등 여러 타입의 데이터를 읽어올 수 있습니다.

### ItemProcessor

- ItemProcessor는 ItemReader로 읽어 온 배치 데이터를 변환하는 역할을 수행합니다. 이것을 분리하는 이유는 다음과 같습니다.
- 비즈니스 로직의 분리 : ItemWriter는 저장 수행하고, ItemProcessor는 로직 처리만 수행해 역할을 명확하게 분리합니다.
- 읽어온 배치 데이터와 씌여질 데이터의 타입이 다를 경우에 대응할 수 있기 때문입니다.

### ItemWriter

- ItemWriter는 배치 데이터를 저장합니다. 일반적으로 DB나 파일에 저장합니다.
- ItemWriter도 ItemReader와 비슷한 방식을 구현합니다. 제네릭으로 원하는 타입을 받고 write() 메서드는 List를 사용해서 저장한 타입의 리스트를 매게변수로 받습니다.