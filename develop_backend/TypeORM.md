# NestJS에 PostgreSQL + TypeORM 적용하기

## 종속 라이브러리 설치

```shell
$> npm install pg typeorm @nestjs/typeorm
```

## 엔티티

엔티티란 ER 모델 (개체-관계 모델)에서 Entity를 의미합니다. 엔티티는 데이터의 객체 혹은 컴포넌트를 의미하며 데이터베이스에 표현하고 저장하고자 하는 것을 의미합니다.

엔티티는 개념적으로 모델링을 한 개념이기 때문에 직접 DB 내에 구현되는 테이블과는 조금 다르며 엔티티가 테이블로 구현되는 관계입니다.

### 엔티티의 구성 요소

엔티티는 속성(어트리뷰트)과 인스턴스로 구성됩니다. 속성은 내부 데이터들의 구분을 짓는 단위이며 인스턴스는 실제로 삽입된 데이터를 의미합니다.

### 엔티티의 식별자

엔티티의 인스턴스를 유일하게 식별할 수 있는 식별자가 존재해야 합니다. 식별자에는 여러 종류가 있지만 엔티티의 인스턴스를 유일하게 식별할 수 있는 키를 Primary Key (기본키) 라고 부릅니다.

### TypeORM

ORM은 객체와 RDB를 매핑시켜주는 것을 의미합니다. TypeORM은 자바스크립트의 객체 (클래스)를 RDB의 테이블에 매핑시켜 줄 수 있습니다.

## 엔티티 구현

TypeORM에서 엔티티는 RDB에 연결할 때엔 `테이블을 클래스에 매핑한 것` 을 의미합니다. TypeORM은 MongoDB처럼 NoSQL도 지원하므로 본질적인 의미는 다소 달라질 수 있지만 데이터베이스에 존재하는 대상을 클래스로 매핑한 의미는 동일합니다.

### 예제

```typescript
import { Entity, PrimaryGeneratedColumn, Column } from "typeorm"

@Entity()
export class User {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    firstName: string

    @Column()
    lastName: string

    @Column()
    isActive: boolean
}
```

엔티티는 클래스로 구현하며 `@Entity()` 데코레이터를 클래스에 달아 엔티티임을 명시합니다. 엔티티의 속성(어트리뷰트)은 클래스의 속성으로 정의하며 속성에 타입을 명시하고  `@Column()` 데코레이터를 이용하여 정의합니다. 속성은 RDB 테이블의 컬럼에 대응됩니다.

### 프라이머리 키 컬럼

프라이머리 키를 설정하는데 다음 두가지 중 하나의 데코레이터를 사용할 수 있습니다.

- `@PrimaryColumn()` : 해당 컬럼을 프라이머리 키로 설정합니다.
- `@PrimaryGeneratedColumn()` : auto-increment 속성을 부여합니다.

### 예제로 생성된 RDB 테이블

```
+-------------+--------------+----------------------------+
|                          user                           |
+-------------+--------------+----------------------------+
| id          | int(11)      | PRIMARY KEY AUTO_INCREMENT |
| firstName   | varchar(255) |                            |
| lastName    | varchar(255) |                            |
| isActive    | boolean      |                            |
+-------------+--------------+----------------------------+
```

### 일반 컬럼

일반 컬럼일 경우 `@Column()` 으로 데코레이터를 설정합니다. 데이터베이스의 컬럼 타입을 좀 더 상세하게 명시하고자 할 때에는 데코레이터 인자에 타입, 길이 등을 삽입할 수 있습니다.

자세한 내용은 https://typeorm.io/entities#column-types 에서 참조할 수 있습니다.

### Enum 컬럼

PostgreSQL이나 MySQL처럼 RDB가 Enum 타입을 지원하는 경우 Enum 컬럼을 적용할 수 있습니다.

먼저 enum 타입을 정의한 후 데코레이터 내에 타입, enum, 기본값을 정의합니다.

```tsx
export enum UserRole {
    ADMIN = "admin",
    EDITOR = "editor",
    GHOST = "ghost",
}

@Entity()
export class User {
    @PrimaryGeneratedColumn()
    id: number

    @Column({
        type: "enum",
        enum: UserRole,
        default: UserRole.GHOST,
    })
    role: UserRole
}
```

### 특수 컬럼 데코레이터

- `@CreateDateColumn()` : date 타입일 경우 알아서 삽입 시점의 날짜를 삽입시켜 줍니다.
- `@UpdateDateColumn()` : date 타입일 경우 알아서 데이터를 수정할 때의 시점의 날짜를 삽입시켜 줍니다.
- `@DeleteDateColumn()` : date 타입일 경우 해당 열을 삭제처리할 때 삭제된 시점의 날짜를 삽입시켜 줍니다.

## Relation

1대일, 1대다, 다대다 설정이 가능합니다.

### Tree Entities

테이블에 대해 인접 목록, 클로저 테이블 패턴을 지원하기 위해 지원합니다. 테이블의 자체 참조 (엔티티 간 관계가 본인의 컬럽으로 이루어진 경우) 등에 대해 본인 엔티티나 다른 테이블 엔티티를 엔티티의 멤버 변수로 넣을 수 있습니다.

### 1:1 관계

1:1 관계는 서로 다른 테이블에 대해 하나의 row만 참조하는 관계입니다.

만약 다음과 같이 서로 다른 엔티티가 정의되있다고 합시다.

```typescript
@Entity()
export class Profile {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    gender: string

    @Column()
    photo: string
}
```

```typescript
@Entity()
export class User {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    name: string
}
```

그러면 다음과 같은 두개의 테이블이 생깁니다.

```
+-------------+--------------+----------------------------+
|                        profile                          |
+-------------+--------------+----------------------------+
| id          | int(11)      | PRIMARY KEY AUTO_INCREMENT |
| gender      | varchar(255) |                            |
| photo       | varchar(255) |                            |
+-------------+--------------+----------------------------+

+-------------+--------------+----------------------------+
|                          user                           |
+-------------+--------------+----------------------------+
| id          | int(11)      | PRIMARY KEY AUTO_INCREMENT |
| name        | varchar(255) |                            |
+-------------+--------------+----------------------------+
```

여기서 user가 profile 테이블의 id를 참조하여 user에 대한 gender, photo를 조회하고자 할 때 외래키를 사용하여야 합니다.

그러면 User 클래스를 다음과 같이 수정해야 합니다.

```typescript
@Entity()
export class User {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    name: string

    @OneToOne(() => Profile) // 관계 설정을 하기 위한 데코레이터입니다.
    @JoinColumn({ name: 'idt' }) // 1:1 관계를 맺고 외래 키를 정의합니다.
    profile: Profile // 참조할 다른 엔티티를 클래스의 프로퍼티로 정의합니다.
}
```

- `JoinColumn` : 컬럼 간 관계 설정을 할 때 사용하며 실질적으로 외래 키 설정을 하는 데코레이터입니다.
  - 테이블의 어느 쪽이 외래 키가 있는 조인 컬럼을 포함하는지 정의합니다.
  - 이 데코레이터를 설정한 엔티티에서 실제 외래 키를 설정합니다.
  - 옵션을 넣지 않으면 자동으로 `참조하는 엔티티 명 + 참조하는 컬럼 명` 으로 컬럼을 만들어 줍니다.
    - 옵션으로 `name` 을 정할 수 있는데 이 옵션은 (현재 엔티티에서) 다른 엔티티를 참조하는 엔티티의 컬럼명을 의미합니다. 이를 별도로 지정하면 이 이름으로 컬럼을 만들어 줍니다.
    - 옵션으로 `referencedColumnName` 도 설정할 수 있는데 이 옵션은 참조하고자 하는 다른 엔티티의 컬럼명을 지정합니다. 보통 두 엔티티의 컬럼명이 동일하므로 설정할 필요가 없습니다.
- `OneToOne` : 컬럼 간 관계를 명시할 때 사용하는 데코레이터입니다.
- `profile: Profile` : 다른 컬럼 (테이블)의 프로퍼티입니다.

