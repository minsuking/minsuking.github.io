---
title: Key Characteristics of Distributed Systems
tags: [System Design]
style:
color:
description: Key Characteristics of a distributed system include Scalability, Reliability, Availability, Efficiency, and Manageability. Let's briefly review them.
---
# Key Characteristics of Distributed Systems   

분산 시스템의 주요 특징으로는 확장 성, 신뢰성, 가용성, 효율성 및 관리성이 있습니다. 간단히 살펴 보겠습니다.<br>
> Key Characteristics of a distributed system include Scalability, Reliability, Availability, Efficiency, and Manageability. Let's briefly review them.<br><br>

### 확장성   
확장성은 시스템, 프로세스 또는 네트워크의 수요 증가 및 관리 기능 증가하는 작업량을 지원하기 위해 지속적으로 진화할 수 있는 모든 분산 시스템은 확장 가능한 것으로 간주된다.<br>

### Scalability
> Scalability is the capability of a system, process, or a network to grow and manage increased demand. Any distributed system that can continuously evolve in order to suppor the growing amount of work is considered to be scalable.<br><br>

시스템은 데이터 볼륨 증가 또는 작업량 증가와 같은 여러 가지 이유로 인해 확장되어야 할 수 있다. 확장 가능한 시스템은 성능 손실 없이 이러한 확장을 달성하기를 원한다.<br>
> A system may have to scale because of many reason like increased data volume or increased amount of work. e.g., number of transctions. A scalable system would like to achieve this scaling without performance loss.<br><br>


일반적으로 시스템의 성능은 확장 가능하도록 설계(또는 청구)되었지만 관리 비용이나 환경 비용 때문에 시스템 크기에 따라 저하된다. 예를 들어, 기계가 서로 멀리 떨어져 있기 때문에 네트워크 속도가 느려질 수 있다. 보다 일반적으로 일부 작업은 고유의 원자성 때문에 또는 시스템 설계에 어떤 결함이 있기 때문에 분산되지 않을 수 있다. 어떤 시점에서, 그러한 업무는 분배에 의해 얻어진 속도 증가를 제한할 것이다. 확장 가능한 아키텍처는 이러한 상황을 피하고 모든 참여 노드의 부하 균형을 균등하게 맞추려고 시도한다.<br>
> Generally, the performance of a system, although designed(or claimed) to be scalable, declines with the system size due to the management or environment cost. For instance, network speed may become slower because machines tend to be far apart from one another . More geneally, some tasks may not be distributed, either because of their inherent atomic nature or because of some flaw in the system design. At some point, such tasks would limit the speed-up obtained by distibution. A scalable architecture avoids this situation and attempts to balace the load on all the participating nodes evenly<br><br>



**수평 vs 수직 스케일링** : 수평적 확장이란 자원 풀에 서버를 더 추가하여 확장하는 것을 의미하며, 수직적 확장이란 기존 서버에 더 많은 전력(CPU, RAM, 스토리지 등)을 추가하여 확장하는 것을 의미한다.<br>
> **Horizontal vs Vertical Scaling** : Horizontal scaling means that you scale by adding more servers into your pool of resources whereas Vertical scaling means that you scale by adding more power(CPU, RAM, Storage, etc.) to an existing server.<br><br>



수평적 스케일링을 사용하면 기존 풀에 시스템을 추가하여 동적으로 확장하기가 더 쉽다. 수직적 스케일링은 대개 단일 서버의 용량으로 제한되며 그 용량을 초과하는 확장에는 다운타임이 수반되는 경우가 많다.<br>
> With horizontal-scaling it is often easier to scale dynamically by adding more machines into the existing pool, Vertical-scaling is usually limited to the capacity of a single server and scaling beyond that capacity often involves downtime and comes with an upper limit.<br><br>


수평적 확장의 좋은 예는 Cassandra와 MongoDB이다. 왜냐하면 둘 다 증가하는 요구를 충족시키기 위해 더 많은 기계를 추가함으로써 수평적으로 확장하는 쉬운 방법을 제공하기 때문이다. 마찬가지로, 수직 스케일링의 좋은 예는 MySQL이다. MySQL은 더 작은 기계에서 더 큰 기계로 전환함으로써 수직 스케일링을 쉽게 할 수 있기 때문이다. 그러나 이 프로세스는 다운타임을 수반하는 경우가 많다.<br>
> Good examples of horizontal scaling are Cassandra and MongoDB as they both provide an easy way to scale horizontally by adding more machines to meet growing needs. similarly, a good example of vertical scaling is MySQL as it allows for an easy way to scale vertically by switching from smaller to bigger machines. However, this process often involves downtime.<br><br>

![architecture](https://1.bp.blogspot.com/-58qlT6bSnC0/VoTvX08YByI/AAAAAAAAGF8/aTJM7229jfA/s400/horizontal-vs-vertical-scaling-vertical-and-horizontal-scaling-explained-diagram.png)

### 신뢰성
정의에 따르면 신뢰성은 특정 기간 내에 시스템이 고장날 확률이다. 간단히 말해서, 분산 시스템은 소프트웨어 또는 하드웨어 구성 요소 중 하나 또는 여러 개에 장애가 발생하더라도 서비스를 계속 제공한다면 신뢰할 수 있는 것으로 간주된다. 신뢰성은 모든 분산 시스템의 주요 특징 중 하나를 나타낸다. 그러한 시스템에서는 고장 난 기계는 항상 다른 정상적인 기계로 교체되어 요청된 작업의 완료를 보장할 수 있기 때문이다.<br>

### Reliability
> By definition, reliability is the probability a system will fail in a given period. In simple terms, a distributed system is considered reliable if it keeps delivering its services even when one or several of its software or hardware comonents fail. Reliability represents one of the main characteristics of any distributed system, since in such systems any failing machine can always be replaced by another healthy one, ensuring the completion of the requested task.<br><br>


큰 전자 상거래 상점(아마존과 같은)을 예로 들어보자. 주요 요구사항 중 하나는 해당 거래를 실행하는 기계의 고장으로 인해 어떠한 사용자 거래도 취소되어서는 안 된다는 것이다. 예를 들어, 사용자가 쇼핑 카트에 물건을 추가한 경우, 시스템은 물건을 잃지 않을 것으로 예상된다. 신뢰할 수 있는 분산 시스템은 소프트웨어 구성 요소와 데이터의 중복성을 통해 이를 달성한다. 사용자의 쇼핑 카트를 운반하는 서버가 고장 나면 쇼핑 카트의 정확한 복제본이 있는 다른 서버가 이를 교체해야 한다.<br>
> Task the example of a large electronic commerce store(like Amazon), where one of the primary requirement is that any user transation should never be canceled due to a failure of the machine that is running that transaction. For instance, if a user has added an item to their shopping cart, the system is expected not to lose it. A reliable distributed system achieves this through redundancy of both the software components and data. If the server carrying the user's shoppping cart fails, another server that has the exact replica of the shopping cart should replace it.<br><br>



물론, 중복성은 비용이 들고, 모든 실패 지점을 제거함으로써 서비스에 대한 그러한 복원력을 달성하기 위해서는 신뢰할 수 있는 시스템이 그 대가를 지불해야 한다.<br>
> Obviously, redundancy has a cost and reliable system has to pay that to achieve such resilience for services by eliminating every single point of failure.<br><br>


### 유용성
정의에 따르면 가용성은 시스템이 특정 기간 동안 필요한 기능을 수행하기 위해 작동 상태를 유지하는 시간이다. 시스템, 서비스 또는 기계가 정상 조건에서 작동 상태를 유지하는 시간의 비율을 나타내는 간단한 측정이다. 한 달에 몇 시간씩 다운타임 없이 비행할 수 있는 항공기는 가용성이 높다고 할 수 있다. 가용성은 유지관리성, 수리 시간, 예비 가용성 및 기타 물류 고려 사항을 고려한다. 항공기가 정비를 위해 정지해 있는 경우, 그 기간 동안 항공기를 이용할 수 없는 것으로 간주한다.<br>
신뢰성이란 발생할 수 있는 가능한 실제 상황의 전체 범위를 고려할 때 시간의 경과에 따른 가용성이다. 어떤 날씨라도 안전하게 통과시킬 수 있는 항공기는 가능한 조건에 대한 취약성이 있는 항공기보다 더 신뢰할 수 있다.<br>

**신뢰도 VS 유용성**<br>
시스템이 신뢰할 수 있으면 이용할 수 있다. 그러나 이용할 수 있다면 반드시 신뢰할 수 있는 것은 아니다. 즉, 높은 신뢰성은 높은 가용성에 기여하지만, 수리 시간을 최소화하고 필요할 때 항상 예비품을 사용할 수 있도록 보장함으로써 신뢰할 수 없는 제품으로도 고가용성을 달성할 수 있다. 출시 후 처음 2년간 99.99%의 가용성을 가진 온라인 소매점을 예로 들어보자. 그러나 이 시스템은 정보보안 테스트 없이 출범했다. 고객들은 이 시스템에 만족하고 있지만, 그들은 그것이 발생할 수 있는 위험에 취약하기 때문에 그다지 신뢰할 수 없다는 것을 알지 못한다. 3년째에, 이 시스템은 일련의 정보보안 사고를 경험하게 되고, 이로 인해 갑자기 장기간에 걸쳐 가용성이 극도로 낮아진다.<br>
