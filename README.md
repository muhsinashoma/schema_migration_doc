<!-----Markdown List----->

#### Discussion Points  
---
1. What is TypeORM entity classes
2. Key concepts in TypeORM entity classes
3. Databae Schema Migration
4. Authentication - user, session, role
5. Authorizarion - menu, rolemenu, authority
6. Payment and billing
7. Logging
8. API Manager
9. Content Information
10. Dependencies
11. Database Migration Category

 #### 1. What is TypeORM entity classes    
---
TypeORM entity classes are TypeScript/JavaScript classes that map to database tables. They use decorators to define the mapping between class properties and database columns.  

#### 2. Key concepts in TypeORM entity classes:   

---

1. Decorators:  

- @Entity(): Marks a class as a database table
- @Column(): Defines a database column
- @PrimaryColumn(): Defines a primary key
- @PrimaryGeneratedColumn(): Auto-generated primary key  

2. Relationships:  

- @OneToOne(): One-to-one relationship
- @OneToMany(): One-to-many relationship
- @ManyToOne(): Many-to-one relationship
- @ManyToMany(): Many-to-many relationship


3. Column Types:  

- Basic types: string, number, boolean
- Database-specific types: varchar, int, float, 
- datetime
- Special types: json, enum, array


4. Column Options:  

- nullable: Allow NULL values
- default: Default value
- length: Column length for string types
- precision and scale: For decimal numbers
- unique: Unique constraint


5. Indices and Constraints:  

- @Index(): Create database index
- @Unique(): Create unique constraint
- Can be applied to single or multiple columns  


#### Here are the key components of TypeORM entity classes with examples:   

```TypeScript
// Example 1: Basic Entity Class
import { Entity, Column, PrimaryColumn } from "typeorm";

@Entity("Users") // Table name in database
export class User {
    @PrimaryColumn({ type: "varchar", length: 50 })
    userId: string;

    @Column({ type: "varchar", length: 100, nullable: true })
    name: string;

    @Column({ type: "varchar", length: 100, nullable: true })
    email: string;
}

// Example 2: Entity with Relationships
import { Entity, PrimaryColumn, Column, OneToMany, ManyToOne, JoinColumn } from "typeorm";

@Entity("Roles")
export class Role {
    @PrimaryColumn({ type: "varchar", length: 50 })
    role: string;

    @Column({ type: "varchar", length: 50, nullable: true })
    description: string;

    // One-to-Many relationship
    @OneToMany(() => UserRole, userRole => userRole.role)
    userRoles: UserRole[];
}

```  
#### 3. Database Schema Migration for OSBD Project  

            - Authentication - user, session, role
---
```TypeScript  

// core/models/user.entity.ts
import { Entity, PrimaryColumn, Column, OneToMany, OneToOne } from 'typeorm';
import { UserSession } from './user-session.entity';
import { UserRole } from './user-role.entity';

@Entity('Users')
export class User {
    @PrimaryColumn({ type: 'varchar', length: 50 })
    userId: string;

    @Column({ type: 'varchar', length: 500, nullable: true })
    pass: string;

    @Column({ type: 'varchar', length: 50, nullable: true })
    userType: string;

    @Column({ type: 'varchar', length: 100, nullable: true })
    name: string;

    @Column({ type: 'varchar', length: 100, nullable: true })
    fullName: string;

    @Column({ type: 'varchar', length: 100, nullable: true })
    designation: string;

    @Column({ type: 'varchar', length: 100, nullable: true })
    department: string;

    @Column({ type: 'varchar', length: 100, nullable: true })
    email: string;

    @Column({ type: 'varchar', length: 50, nullable: true })
    contactNum: string;

    @Column({ type: 'varchar', length: 100, nullable: true })
    photo: string;

    @Column({ type: 'varchar', length: 50, nullable: true })
    updatedBy: string;

    @Column({ type: 'datetime', nullable: true })
    lastUpdate: Date;

    @Column({ type: 'datetime', nullable: true })
    expiry: Date;

    @Column({ type: 'varchar', length: 50, nullable: true })
    status: string;

    @Column({ type: 'varchar', length: 50, nullable: true })
    authType: string;

    @OneToMany(() => UserSession, session => session.user)
    sessions: UserSession[];

    @OneToMany(() => UserRole, userRole => userRole.user)
    userRoles: UserRole[];
}

// core/models/user-session.entity.ts
import { Entity, PrimaryGeneratedColumn, Column, ManyToOne, JoinColumn } from 'typeorm';
import { User } from './user.entity';

@Entity('userSessions')
export class UserSession {
    @PrimaryGeneratedColumn({ type: 'bigint' })
    sessionId: number;

    @Column({ type: 'varchar', length: 50, nullable: true })
    userId: string;

    @Column({ type: 'varchar', length: 500, nullable: true })
    reqSession: string;

    @Column({ type: 'varchar', length: 'max', nullable: true })
    sessionInfo: string;

    @Column({ type: 'datetime', nullable: true })
    startTime: Date;

    @Column({ type: 'datetime', nullable: true })
    endTime: Date;

    @Column({ type: 'datetime', nullable: true })
    lastActivity: Date;

    @Column({ type: 'varchar', length: 'max', nullable: true })
    endReason: string;

    @Column({ type: 'varchar', length: 50, nullable: true })
    sessionStatus: string;

    @ManyToOne(() => User, user => user.sessions)
    @JoinColumn({ name: 'userId' })
    user: User;
}

// core/models/role.entity.ts
import { Entity, PrimaryColumn, Column, OneToMany } from 'typeorm';
import { UserRole } from './user-role.entity';
import { RoleMenu } from './role-menu.entity';
import { RoleAuthority } from './role-authority.entity';

@Entity('Roles')
export class Role {
    @PrimaryColumn({ type: 'varchar', length: 50 })
    role: string;

    @Column({ type: 'varchar', length: 50, nullable: true })
    home: string;

    @Column({ type: 'int', nullable: true })
    priority: number;

    @Column({ type: 'int', nullable: true })
    maxSessions: number;

    @Column({ type: 'int', nullable: true })
    inactivityTimeout: number;

    @Column({ type: 'int', nullable: true })
    passwordFailLimit: number;

    @Column({ type: 'int', nullable: true })
    nFAFailLimit: number;

    @OneToMany(() => UserRole, userRole => userRole.role)
    userRoles: UserRole[];

    @OneToMany(() => RoleMenu, roleMenu => roleMenu.role)
    roleMenus: RoleMenu[];

    @OneToMany(() => RoleAuthority, roleAuthority => roleAuthority.role)
    roleAuthorities: RoleAuthority[];
}

// core/models/user-role.entity.ts
import { Entity, PrimaryGeneratedColumn, Column, ManyToOne, JoinColumn } from 'typeorm';
import { User } from './user.entity';
import { Role } from './role.entity';

@Entity('UserRole')
export class UserRole {
    @PrimaryGeneratedColumn({ type: 'bigint' })
    id: number;

    @Column({ type: 'varchar', length: 50, nullable: true })
    userId: string;

    @Column({ type: 'varchar', length: 50, nullable: true })
    role: string;

    @ManyToOne(() => User, user => user.userRoles)
    @JoinColumn({ name: 'userId' })
    user: User;

    @ManyToOne(() => Role, role => role.userRoles)
    @JoinColumn({ name: 'role' })
    roleRef: Role;
}  
```


#### Authorizarion - menu, rolemenu, authority      
 --- 

 ```TypeScript  
// core/models/menu-def.entity.ts
import { Entity, PrimaryColumn, Column, OneToMany } from 'typeorm';
import { RoleMenu } from './role-menu.entity';
import { MenuAuthority } from './menu-authority.entity';

@Entity('menuDef')
export class MenuDef {
    @PrimaryColumn({ type: 'varchar', length: 50 })
    menuId: string;

    @Column({ type: 'varchar', length: 50, nullable: true })
    parent: string;

    @Column({ type: 'varchar', length: 'max', nullable: true })
    menuURL: string;

    @Column({ type: 'varchar', length: 50, nullable: true })
    cssParent: string;

    @Column({ type: 'varchar', length: 50, nullable: true })
    cssClass: string;

    @Column({ type: 'varchar', length: 50, nullable: true })
    aClass: string;

    @Column({ type: 'varchar', length: 50, nullable: true })
    dataToggle: string;

    @Column({ type: 'varchar', length: 50, nullable: true })
    context: string;

    @Column({ type: 'int', nullable: true })
    slNum: number;

    @OneToMany(() => RoleMenu, roleMenu => roleMenu.menuDef)
    roleMenus: RoleMenu[];

    @OneToMany(() => MenuAuthority, menuAuthority => menuAuthority.menuDef)
    menuAuthorities: MenuAuthority[];
}

// core/models/role-menu.entity.ts
import { Entity, Column, ManyToOne, JoinColumn } from 'typeorm';
import { Role } from './role.entity';
import { MenuDef } from './menu-def.entity';

@Entity('RoleMenu')
export class RoleMenu {
    @Column({ type: 'varchar', length: 50 })
    role: string;

    @Column({ type: 'varchar', length: 50 })
    menuId: string;

    @ManyToOne(() => Role, role => role.roleMenus)
    @JoinColumn({ name: 'role' })
    roleRef: Role;

    @ManyToOne(() => MenuDef, menuDef => menuDef.roleMenus)
    @JoinColumn({ name: 'menuId' })
    menuDef: MenuDef;
}

// core/models/role-authority.entity.ts
import { Entity, PrimaryGeneratedColumn, Column, ManyToOne, JoinColumn } from 'typeorm';
import { Role } from './role.entity';

@Entity('roleAuthority')
export class RoleAuthority {
    @PrimaryGeneratedColumn({ type: 'bigint' })
    id: number;

    @Column({ type: 'varchar', length: 50, nullable: true })
    role: string;

    @Column({ type: 'varchar', length: 50, nullable: true })
    authorityId: string;

    @Column({ type: 'int', nullable: true })
    canView: number;

    @Column({ type: 'int', nullable: true })
    canChange: number;

    @Column({ type: 'int', nullable: true })
    canDelete: number;

    @ManyToOne(() => Role, role => role.roleAuthorities)
    @JoinColumn({ name: 'role' })
    roleRef: Role;
}

// core/models/menu-authority.entity.ts
import { Entity, PrimaryGeneratedColumn, Column, ManyToOne, JoinColumn } from 'typeorm';
import { MenuDef } from './menu-def.entity';

@Entity('menuAuthority')
export class MenuAuthority {
    @PrimaryGeneratedColumn({ type: 'bigint' })
    id: number;

    @Column({ type: 'varchar', length: 50, nullable: true })
    menuId: string;

    @Column({ type: 'varchar', length: 50, nullable: true })
    authorityId: string;

    @ManyToOne(() => MenuDef, menuDef => menuDef.menuAuthorities)
    @JoinColumn({ name: 'menuId' })
    menuDef: MenuDef;
}
```

#### Payment and Billing  
---
```TypeScript
// core/models/payment.entity.ts
import { Entity, PrimaryColumn, Column, ManyToOne, JoinColumn } from 'typeorm';
import { Service } from './service.entity';
import { RateCode } from './rate-code.entity';

@Entity('payment')
export class Payment {
    @PrimaryColumn({ type: 'varchar', length: 100 })
    paymentId: string;

    @Column({ type: 'varchar', length: 100, nullable: true })
    paymentStatus: string;

    @Column({ type: 'datetime', nullable: false })
    creationDateTime: Date;

    @Column({ type: 'varchar', length: 100, nullable: false })
    serviceId: string;

    @Column({ type: 'varchar', length: 100, nullable: true })
    rateCodeId: string;

    @Column({ type: 'varchar', length: 100, nullable: true })
    rateCodeGroupId: string;

    @Column({ type: 'varchar', length: 100, nullable: true })
    paymentMethodId: string;

    @Column({ type: 'varchar', length: 200, nullable: false })
    customerId: string;

    @Column({ type: 'float', nullable: true })
    amount: number;

    @ManyToOne(() => Service)
    @JoinColumn({ name: 'serviceId' })
    service: Service;

    @ManyToOne(() => RateCode)
    @JoinColumn({ name: 'rateCodeId' })
    rateCode: RateCode;
}

// core/models/service.entity.ts
import { Entity, PrimaryColumn, Column, OneToMany } from 'typeorm';
import { Payment } from './payment.entity';

@Entity('service')
export class Service {
    @PrimaryColumn({ type: 'varchar', length: 100 })
    serviceId: string;

    @Column({ type: 'varchar', length: 200, nullable: true })
    serviceCode: string;

    @Column({ type: 'varchar', length: 100, nullable: true })
    serviceName: string;

    @OneToMany(() => Payment, payment => payment.service)
    payments: Payment[];
}

// core/models/payment-method.entity.ts

import { Entity, PrimaryColumn, Column, OneToMany } from "typeorm";
import { Payment } from "./payment.entity";

@Entity("paymentmethods")
export class PaymentMethod {
    @PrimaryColumn({ type: "varchar", length: 100 })
    paymentMethodId: string;

    @Column({ type: "varchar", length: 200, nullable: true })
    paymentMethodCode: string;

    @Column({ type: "varchar", length: 200, nullable: true })
    paymentMethodName: string;

    @Column({ type: "varchar", length: "max", nullable: true })
    paymentMethodLogoURL: string;

    @OneToMany(() => Payment, payment => payment.paymentMethod)
    payments: Payment[];
}

// core/models/rate-code.entity.ts
import { Entity, PrimaryColumn, Column } from 'typeorm';

@Entity('ratecodes')
export class RateCode {
    @PrimaryColumn({ type: 'varchar', length: 100 })
    rateCodeId: string;

    @Column({ type: 'varchar', length: 200, nullable: true })
    rateCode: string;

    @Column({ type: 'varchar', length: 100, nullable: true })
    rateCodeGroupId: string;

    @Column({ type: 'varchar', length: 200, nullable: true })
    rateName: string;

    @Column({ type: 'varchar', length: 100, nullable: true })
    rateDesc: string;

    @Column({ type: 'varchar', length: 50, nullable: true })
    paymentMethodId: string;

    @Column({ type: 'varchar', length: 200, nullable: true })
    discountCodes: string;

    @Column({ type: 'int', nullable: true })
    priority: number;

    @Column({ type: 'varchar', length: 'max', nullable: true })
    rateCodeLogoURL: string;

    @Column({ type: 'datetime', nullable: false })
    creationDateTime: Date;

    @Column({ type: 'varchar', length: 200, nullable: true })
    status: string;

    @Column({ type: 'datetime', nullable: true })
    expirationDate: Date;

    @Column({ type: 'int', nullable: true })
    subscriptionDuration: number;

    @Column({ type: 'float', nullable: true })
    amount: number;

    @Column({ type: 'varchar', length: 50, nullable: true })
    rateType: string;

    @Column({ type: 'varchar', length: 100, nullable: true })
    category: string;
}
```  

#### Logging  
---

```TypeScript
// core/models/log.entity.ts
import { Entity, PrimaryGeneratedColumn, Column } from 'typeorm';

@Entity('log')
export class Log {
    @PrimaryGeneratedColumn({ type: 'bigint' })
    EventID: number;

    @Column({ type: 'varchar', length: 50, nullable: false })
    EventNumber: string;

    @Column({ type: 'varchar', length: 4000, nullable: true })
    EventDescription: string;

    @Column({ type: 'varchar', length: 100, nullable: true })
    EventProcedure: string;

    @Column({ type: 'int', nullable: true })
    EventState: number;

    @Column({ type: 'int', nullable: true })
    EventSeverity: number;

    @Column({ type: 'int', nullable: true })
    EventLine: number;

    @Column({ type: 'datetime', nullable: true })
    EventTime: Date;
}
```

#### API  
---

```TypeScript
// core/models/api.entity.ts
import { Entity, Column } from 'typeorm';

@Entity('api')
export class Api {
    @Column({ type: 'varchar', length: 50, nullable: false })
    svcEndPoint: string;

    @Column({ type: 'varchar', length: 50, nullable: true })
    svcType: string;

    @Column({ type: 'text', nullable: true })
    svcParam: string;

    @Column({ type: 'text', nullable: true })
    svcData: string;
}



# 1. First, create typeorm config file (ormconfig.ts):  

import { DataSource } from "typeorm"

export const AppDataSource = new DataSource({
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "your_username",
    password: "your_password",
    database: "your_database",
    synchronize: false, // Set to false in production
    logging: true,
    entities: ["src/entities/**/*.ts"],
    migrations: ["src/migrations/**/*.ts"],
    subscribers: ["src/subscribers/**/*.ts"],
})

# 2. Add scripts to package.json:

{
    "scripts": {
        "typeorm": "typeorm-ts-node-commonjs",
        "migration:generate": "npm run typeorm migration:generate src/migrations/initial",
        "migration:run": "npm run typeorm migration:run -- -d src/data-source.ts",
        "migration:revert": "npm run typeorm migration:revert -- -d src/data-source.ts"
    }
}

# 3. Create the TypeORM entity (src/entities/api.entity.ts):

import { Entity, Column } from "typeorm"

@Entity("api")
export class Api {
    @Column("varchar", { length: 50, nullable: false })
    svcEndPoint: string;

    @Column("varchar", { length: 50, nullable: true })
    svcType: string;

    @Column("text", { nullable: true })
    svcParam: string;

    @Column("text", { nullable: true })
    svcData: string;

    @Column("varchar", { length: 50, nullable: true })
    passSessionInfo: string;

    @Column("varchar", { length: 500, nullable: true })
    purpose: string;

    @Column("datetime", { nullable: true })
    lastUpdated: Date;

    @Column("bigint", { nullable: true })
    hitCount: number;

    @Column("datetime", { nullable: true })
    lastAccess: Date;

    @Column("text", { nullable: true })
    lastAccessFrom: string;

    @Column("text", { nullable: true })
    lastResult: string;

    @Column("int", { nullable: true })
    allowAuthToken: number;

    @Column("int", { nullable: true })
    requireSession: number;

    @Column("varchar", { length: 50, nullable: true })
    funcArea: string;

    @Column("varchar", { length: 50, nullable: true })
    module: string;

    @Column("varchar", { length: 500, nullable: true })
    exampleURL: string;

    @Column("varchar", { length: 500, nullable: true })
    exampleResult: string;

    @Column("varchar", { length: 500, nullable: true })
    referenceTables: string;

    @Column("varchar", { length: 500, nullable: true })
    usageGuideline: string;

    @Column("int", { nullable: true })
    enforceUserIdentity: number;
}

# 4. Generate the migration:

npm run migration:generate

# This will create a file like: src/migrations/TIMESTAMP-initial.ts

# 5. The generated migration will look like this:

import { MigrationInterface, QueryRunner } from "typeorm";

export class Initial1642523695459 implements MigrationInterface {
    name = 'Initial1642523695459'

    public async up(queryRunner: QueryRunner): Promise<void> {
        await queryRunner.query(`
            CREATE TABLE \`api\` (
                \`svcEndPoint\` varchar(50) NOT NULL,
                \`svcType\` varchar(50) NULL,
                \`svcParam\` text NULL,
                \`svcData\` text NULL,
                \`passSessionInfo\` varchar(50) NULL,
                \`purpose\` varchar(500) NULL,
                \`lastUpdated\` datetime NULL,
                \`hitCount\` bigint NULL,
                \`lastAccess\` datetime NULL,
                \`lastAccessFrom\` text NULL,
                \`lastResult\` text NULL,
                \`allowAuthToken\` int NULL,
                \`requireSession\` int NULL,
                \`funcArea\` varchar(50) NULL,
                \`module\` varchar(50) NULL,
                \`exampleURL\` varchar(500) NULL,
                \`exampleResult\` varchar(500) NULL,
                \`referenceTables\` varchar(500) NULL,
                \`usageGuideline\` varchar(500) NULL,
                \`enforceUserIdentity\` int NULL
            ) ENGINE=InnoDB
        `);

        // Add indices
        await queryRunner.query(`
            CREATE INDEX \`IDX_API_ENDPOINT\` ON \`api\` (\`svcEndPoint\`)
        `);

        await queryRunner.query(`
            CREATE INDEX \`IDX_API_MODULE\` ON \`api\` (\`module\`, \`funcArea\`)
        `);
    }

    public async down(queryRunner: QueryRunner): Promise<void> {
        await queryRunner.query(`DROP INDEX \`IDX_API_MODULE\` ON \`api\``);
        await queryRunner.query(`DROP INDEX \`IDX_API_ENDPOINT\` ON \`api\``);
        await queryRunner.query(`DROP TABLE \`api\``);
    }
}

# 6. Run the migration:

npm run migration:run

# 7. To revert the migration if needed:

npm run migration:revert
```  
#### Content Information/Details 
---
```TypeScript
// core/models/content.entity.ts
import { Entity, PrimaryColumn, Column, OneToMany, ManyToOne, JoinColumn } from 'typeorm';
import { ContentCategory } from './content-category.entity';
import { ContentTag } from './content-tag.entity';
import { ContentComment } from './content-comment.entity';
import { User } from './user.entity';

@Entity('Contents')
export class Content {
    @PrimaryColumn({ type: 'varchar', length: 50 })
    content_id: string;

    @Column({ type: 'varchar', length: 255 })
    title: string;

    @Column({ type: 'text' })
    body: string;

    @Column({ type: 'varchar', length: 255, nullable: true })
    slug: string;

    @Column({ type: 'varchar', length: 100, nullable: true })
    excerpt: string;

    @Column({ type: 'varchar', length: 255, nullable: true })
    featured_image: string;

    @Column({ type: 'varchar', length: 20, default: 'draft' })
    status: string;

    @Column({ type: 'boolean', default: false })
    is_featured: boolean;

    @Column({ type: 'int', default: 0 })
    view_count: number;

    @Column({ type: 'timestamp', default: () => 'CURRENT_TIMESTAMP' })
    created_at: Date;

    @Column({ type: 'timestamp', nullable: true })
    updated_at: Date;

    @Column({ type: 'varchar', length: 50 })
    created_by: string;

    @ManyToOne(() => User, user => user.contents)
    @JoinColumn({ name: 'created_by' })
    user: User;

    @OneToMany(() => ContentTag, contentTag => contentTag.content)
    contentTags: ContentTag[];

    @OneToMany(() => ContentComment, contentComment => contentComment.content)
    contentComments: ContentComment[];

    @ManyToOne(() => ContentCategory, category => category.contents)
    @JoinColumn({ name: 'category_id' })
    category: ContentCategory;

    @Column({ type: 'varchar', length: 50 })
    category_id: string;
}

// core/models/content-category.entity.ts
@Entity('ContentCategories')
export class ContentCategory {
    @PrimaryColumn({ type: 'varchar', length: 50 })
    category_id: string;

    @Column({ type: 'varchar', length: 100 })
    name: string;

    @Column({ type: 'varchar', length: 255, nullable: true })
    description: string;

    @Column({ type: 'varchar', length: 100, nullable: true })
    slug: string;

    @Column({ type: 'timestamp', default: () => 'CURRENT_TIMESTAMP' })
    created_at: Date;

    @Column({ type: 'timestamp', nullable: true })
    updated_at: Date;

    @Column({ type: 'varchar', length: 50 })
    created_by: string;

    @ManyToOne(() => User, user => user.contentCategories)
    @JoinColumn({ name: 'created_by' })
    user: User;

    @OneToMany(() => Content, content => content.category)
    contents: Content[];
}

// core/models/content-tag.entity.ts
@Entity('ContentTags')
export class ContentTag {
    @PrimaryColumn({ type: 'varchar', length: 50 })
    content_id: string;

    @PrimaryColumn({ type: 'varchar', length: 50 })
    tag_id: string;

    @Column({ type: 'timestamp', default: () => 'CURRENT_TIMESTAMP' })
    created_at: Date;

    @Column({ type: 'varchar', length: 50 })
    created_by: string;

    @ManyToOne(() => Content, content => content.contentTags)
    @JoinColumn({ name: 'content_id' })
    content: Content;

    @ManyToOne(() => User, user => user.contentTags)
    @JoinColumn({ name: 'created_by' })
    user: User;
}

// core/models/content-comment.entity.ts
@Entity('ContentComments')
export class ContentComment {
    @PrimaryColumn({ type: 'varchar', length: 50 })
    comment_id: string;

    @Column({ type: 'varchar', length: 50 })
    content_id: string;

    @Column({ type: 'text' })
    comment: string;

    @Column({ type: 'timestamp', default: () => 'CURRENT_TIMESTAMP' })
    created_at: Date;

    @Column({ type: 'timestamp', nullable: true })
    updated_at: Date;

    @Column({ type: 'varchar', length: 50 })
    created_by: string;

    @ManyToOne(() => Content, content => content.contentComments)
    @JoinColumn({ name: 'content_id' })
    content: Content;

    @ManyToOne(() => User, user => user.contentComments)
    @JoinColumn({ name: 'created_by' })
    user: User;
}
```  

#### Dependencies  
--- 

1. Core dependencies  
npm install typeorm reflect-metadata @types/node typescript ts-node mysql2

2. Development dependencies  
npm install -D @types/node @types/reflect-metadata typescript ts-node


#### Database Migration Category  
--- 

1. __Database System Migration:__ Moving data between different database management systems (e.g., from MySQL to PostgreSQL).

2. __Server Migration:__ Transferring a database from one server to another within the same DBMS.

3. __Schema Migration:__ Modifying the database schema, such as altering tables, indexes, or relationships, to accommodate application changes.

4. __Data Migration:__ Moving and transforming data while ensuring data integrity and consistency.  



#### ACID 
---
__What is ACID in Databases?__  

ACID stands for Atomicity, Consistency, Isolation, and Durability, which are a set of properties that guarantee reliable and consistent transactions in a database system. These properties ensure that even in the presence of failures (e.g., system crashes, power outages, etc.), the database maintains data integrity and correctness.  

#### ACID Properties  
---
__Atomicity__

Definition: A transaction is treated as a single, indivisible unit of work. Either all operations within the transaction are completed successfully, or none of them are applied to the database.
Example: In a banking system, transferring $500 from Account A to Account B involves two steps:
Debit $500 from Account A.
Credit $500 to Account B.
If one step fails (e.g., system crash during step 2), the transaction is rolled back, and no changes are applied.

__Consistency__

Definition: The database transitions from one valid state to another valid state after a transaction. All rules, constraints, and relationships defined in the database are maintained.
Example: If a database enforces a rule that "account balance cannot be negative," no transaction should violate this rule.

__Isolation__

Definition: Transactions are executed in isolation from one another. Concurrent transactions do not interfere with each other, and their intermediate states are not visible to other transactions.
Example: If two users are updating the same account simultaneously, their transactions are isolated so that one does not see the partial updates of the other.
Isolation Levels (defined by SQL standards):

Read Uncommitted: Transactions can see uncommitted changes from other transactions.
Read Committed: Transactions only see committed changes.
Repeatable Read: Ensures consistent results for the same query within a transaction.
Serializable: Highest level of isolation; ensures no conflicting transactions.

__Durability__

Definition: Once a transaction is committed, its changes are permanently recorded in the database, even in the event of a system failure.
Example: After a successful fund transfer, the system crashes. When it recovers, the transfer will still be reflected in the account balances.

__Why is ACID Important?__  
ACID properties are critical for:

Data Integrity: Ensures the database remains accurate and reliable.
Fault Tolerance: Protects data in case of crashes or power outages.
Concurrency Control: Manages multiple users accessing the database simultaneously.
Business Rules Enforcement: Prevents invalid states or data inconsistencies.  


