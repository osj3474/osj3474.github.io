---
title: \[Web\] 한 테이블에 데이터가 많으면
excerpt: "DB partitioning"
categories:
   - web
---

<br><br>


# 시작하며

----------------------------------------------

rdb는 scale out이 불가능하다.

그래서 분할DB를 쓴다.

connect storage engine도 알게 되었다.



그런데, 특정 테이블에 데이터가 몰려 있을 때는 어떻게 해야할까?

테이블을 분할해도 좋겠지만, 비용(정규화 이슈, 추후 join)발생이 우려된다.

파티셔닝을 해보자.

// 메일 사서함은 용량이 3GB로 제한되어 있고, 그래서 보관주기를 설정해둔다. 90일

그러면 어차피 여러 파티션을 건드릴 일이 거의 없는 것이다.

cf) 파티셔닝 코스트가 큰 경우 : 





사용이유

: 한 테이블에 데이터가 너무 쌓임



원리

: 한 테이블 데이터를 여러 테이블로 분산 (select에는 지장이 없다.)



사용전 확인할 사항

1. 100row 이상일 때 사용하기 (현재 1100rows)
2. 파티션 컬럼을 지정 (PK를 반드시 포함해야한다.)
3. RANGE, List, Hash 등을 이용한다.

이벤트 스케줄러에 등록하자.

주의: 쿼리 속도를 위해서라면 인덱스(BTree등)부터 고려해야한다.



```mariadb
-- 샘플 테이블 생성 
create table `mail`(
	`id` int not null,
    `adrTo` varchar(255),
    `adrFrom` varchar(255),
    `bodyText` varchar(255),
    `date` datetime,
    PRIMARY KEY (`id`, `date`)
);

-- 샘플 데이터 넣기
insert into `mail`(`id`, `adrTo`, `adrFrom`, `bodyText`, `date`) values(1, 'user1@gmail.com', 'user2@gmail.com', '나는 오늘 컵밥 먹을거야.', '2021-01-15 14:17:40');
insert into `mail`(`id`, `adrTo`, `adrFrom`, `bodyText`, `date`) values(2, 'user10@gmail.com', 'user56@gmail.com', '나는 오늘 치킨 먹을거야.', '2021-01-18 22:17:40');
insert into `mail`(`id`, `adrTo`, `adrFrom`, `bodyText`, `date`) values(3, 'user4@gmail.com', 'user25@gmail.com', '나는 오늘 피자 먹을거야.', '2021-02-01 12:16:13');
insert into `mail`(`id`, `adrTo`, `adrFrom`, `bodyText`, `date`) values(4, 'user71@gmail.com', 'user257@gmail.com', '나는 오늘 도시락 먹을거야.', '2021-02-17 14:17:24');
insert into `mail`(`id`, `adrTo`, `adrFrom`, `bodyText`, `date`) values(5, 'user115@gmail.com', 'user255@gmail.com', '나는 오늘 자장면 먹을거야.', '2021-03-08 14:17:50');
insert into `mail`(`id`, `adrTo`, `adrFrom`, `bodyText`, `date`) values(6, 'user17@gmail.com', 'user24@gmail.com', '나는 오늘 족발 먹을거야.', '2021-04-09 14:17:30');
insert into `mail`(`id`, `adrTo`, `adrFrom`, `bodyText`, `date`) values(7, 'user141@gmail.com', 'user572@gmail.com', '나는 오늘 파스타 먹을거야.', '2021-04-11 14:20:45');
insert into `mail`(`id`, `adrTo`, `adrFrom`, `bodyText`, `date`) values(8, 'user18@gmail.com', 'user25@gmail.com', '나는 오늘 볶음밥 먹을거야.', '2021-05-21 12:17:40');
insert into `mail`(`id`, `adrTo`, `adrFrom`, `bodyText`, `date`) values(9, 'user15@gmail.com', 'user42@gmail.com', '나는 오늘 떡볶이 먹을거야.', '2021-06-13 17:17:40');
insert into `mail`(`id`, `adrTo`, `adrFrom`, `bodyText`, `date`) values(10, 'user177@gmail.com', 'user242@gmail.com', '나는 오늘 순대 먹을거야.', '2021-06-14 13:17:40');

-- 데이터 확인
select * from `mail`;

-- 애초에 파티션 DB를 만드는 경우
CREATE TABLE `mail_partition`(
    `id` int not null,
    `adrTo` varchar(255),
    `adrFrom` varchar(255),
    `bodyText` varchar(255),
    `date` date,
    PRIMARY KEY (`id`, `date`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4
partition by range columns(`date`) (
	partition p202101 VALUES LESS THAN('2021-01-01') ENGINE=InnoDB,
	partition p202102 VALUES LESS THAN('2021-02-01') ENGINE=InnoDB,
	partition p202103 VALUES LESS THAN('2021-03-01') ENGINE=InnoDB,
	partition p202104 VALUES LESS THAN('2021-04-01') ENGINE=InnoDB,
	partition p202105 VALUES LESS THAN('2021-05-01') ENGINE=InnoDB,
	partition pnow VALUES LESS THAN MAXVALUE ENGINE=InnoDB
);

-- 기존의 DB를 파티셔닝하는 경우
alter table `mail` partition by range columns(`date`)(
	partition p202101 VALUES LESS THAN('2021-02-01') ENGINE=InnoDB,
	partition p202102 VALUES LESS THAN('2021-03-01') ENGINE=InnoDB,
	partition p202103 VALUES LESS THAN('2021-04-01') ENGINE=InnoDB,
	partition p202104 VALUES LESS THAN('2021-05-01') ENGINE=InnoDB,
	partition pnow VALUES LESS THAN MAXVALUE ENGINE=InnoDB
);

-- 파티셔닝 확인
SELECT TABLE_SCHEMA, TABLE_NAME, PARTITION_NAME, PARTITION_ORDINAL_POSITION, TABLE_ROWS
FROM INFORMATION_SCHEMA.PARTITIONS
WHERE TABLE_NAME = 'mail';

-- 파티션별 확인
select * from `mail` partition(p202102);
```






<br /><br /><br />
