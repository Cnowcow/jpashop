(findMember == member)가 true가 나오는 이유는 JPA의 1차 캐시와 엔티티 동일성 보장 때문입니다. 이를 이해하기 위해서는 JPA가 엔티티를 어떻게 관리하고, EntityManager가 어떤 역할을 하는지 알아야 합니다.

1. 1차 캐시와 엔티티 관리
1차 캐시는 EntityManager가 관리하는 메모리 내의 캐시입니다. 엔티티를 조회하거나, 저장할 때 EntityManager는 해당 엔티티를 1차 캐시에 저장하고 관리합니다.
EntityManager를 통해 동일한 @Transactional 범위 내에서 동일한 엔티티를 다시 조회하게 되면, 데이터베이스를 다시 조회하지 않고 1차 캐시에 저장된 엔티티를 반환합니다.
JPA에서는 같은 EntityManager 내에서 동일한 식별자를 가진 엔티티는 항상 동일한 인스턴스로 관리됩니다. 따라서, 두 번 조회하더라도 동일한 객체(참조)를 반환하게 됩니다.
2. 실제 코드 동작
memberRepository.save(member)를 호출하면, EntityManager는 member 객체를 데이터베이스에 persist하고, 동시에 1차 캐시에 해당 객체를 저장합니다.
이후 memberRepository.find(savedId)를 호출하면, EntityManager는 먼저 1차 캐시에서 해당 ID를 가진 엔티티가 있는지 확인합니다. 이때 1차 캐시에 이미 member가 저장되어 있으므로, 데이터베이스에서 다시 조회하지 않고, 1차 캐시에 있는 member 객체를 그대로 반환합니다.
따라서 findMember와 member는 동일한 인스턴스(객체 참조)를 가리키게 되며, (findMember == member)는 true가 됩니다.
3. 동일성 보장
JPA는 트랜잭션 내에서 같은 ID를 가진 엔티티는 동일한 객체로 보장해줍니다. 이를 통해 애플리케이션은 엔티티의 상태 변화를 일관되게 추적하고 관리할 수 있습니다.
즉, == 연산자를 사용하여 객체의 동일성을 확인할 수 있는 것은 JPA가 같은 트랜잭션 내에서 동일한 엔티티를 캐시를 통해 관리하기 때문입니다.
4. 요약
findMember == member가 true로 나오는 이유는 JPA의 1차 캐시로 인해 동일한 트랜잭션 내에서 동일한 엔티티가 동일한 인스턴스로 관리되기 때문입니다.
JPA는 EntityManager의 1차 캐시를 통해 동일한 트랜잭션 내에서 엔티티를 관리하고, 이로 인해 메모리에서 동일한 객체 참조를 유지하게 됩니다.
이런 동작 방식 덕분에 JPA를 사용할 때, 효율적으로 엔티티를 관리하고 성능을 높일 수 있습니다.
