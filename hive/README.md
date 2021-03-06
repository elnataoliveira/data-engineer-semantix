### copying files from local to hdfs

``` sh
docker exec -it namenode bash
ls /input/exercises-data/populacaoLA/
hdfs dfs -mkdir /user/aluno/elnataoliveira/data/populacao
hdfs dfs -put /input/exercises-data/populacaoLA/populacaoLA.csv /user/aluno/elnataoliveira/data/populacao
hdfs dfs -ls /user/aluno/elnataoliveira/data/populacao
hdfs dfs -cat /user/aluno/elnataoliveira/data/populacao/populacaoLA.csv | head -n 3

```

### list databases on hive
``` sh
docker exec -it hive-server bash
beeline --help
beeline -u jdcb:hive2://localhost:10000
show databases;
create database elnataoliveira;
show databases;
use elnataoliveira
#create table elnataoliveira.pop
create table pop(
  zip_code int,
  total_population int,
  median_age float,
  total_males int,
  total_females int,
  total_households int,
  average_household_size float
)
  row format delimited
  fields terminated by ','
  lines terminated by '\n'
  stored as textfile
  tblproperties("skip.header.line.count"="1");
  desc pop;
  desc formatted pop;
  
```
### insert data on hive database

``` sh
docker exec -it hive-server bash
beeline -u jdbc:hive2://localhost:10000
show databases;
use elnataoliveira;
show tables;
desc formatted pop;
load data inpath '/user/aluno/elnataoliveira/data/populacao' overwrite into table pop;
select * from pop limit 10;
select count(*) from pop;

```

``` sh
msck repair table <nomeTable>

alter table <nameTable> add partition(<partition>='<valor>');

alter table logs add partition(data='2019-21-02');

docker exec -it namenode hdfs dfs -mkdir /user/aluno/elnataoliveira/data/nascimento
docker exec -it namenode hdfs dfs -ls /user

docker exec -it hive-server bash
beeline -u jdbc:hive2://localhost:10000


create external table nascimento(
  nome string,
  sexo string,
  frequencia int
)
  partitioned by (ano int)
  row format delimited
  fields terminated by ','
  lines terminated by '\n'
  stored as textfile
  location '/user/aluno/elnataoliveira/data/nascimento';
  

alter table nascimento add partition(ano=2015);

docker exec -it namenode hdfs -ls /user/aluno/elnataoliveira/nascimento
docker exec -it namenode hdfs dfs -put input/exercises-data/names/yob2015.txt /user/aluno/elnataoliveira/data/nascimento/ano=2015

docker exec -it hive-server bash
beeline -u jdbc:hive2://localhost:10000
select * from nascimento;
 
alter table nascimento add partition(ano=2016);
alter table nascimento add partition(ano=2017);
 
docker exec -it namenode hdfs dfs -put input/exercises-data/names/yob2016.txt /user/aluno/elnataoliveira/data/nascimento/ano=2016
docker exec -it namenode hdfs dfs -put input/exercises-data/names/yob2017.txt /user/aluno/elnataoliveira/data/nascimento/ano=2017
docker exec -it namenode hdfs -ls /user/aluno/elnataoliveira/nascimento

docker exec -it hive-server bash
beeline -u jdbc:hive2://localhost:10000
select * from nascimento where ano = 2015 limit 10;
select * from nascimento where ano = 2017 limit 10;
select * from nascimento where ano = 2017 limit 10;

```
### optimized tables
``` sh

create table pop_parquet(
  zip_code int,
  total_population int,
  median_age float,
  total_males int,
  total_females int,
  total_households int,
  average_household_size float
)
stored as parquet;

insert into pop_parquet select * from pop;

select count(*) from pop;
select count(*) from pop_parquet;

select * from pop_parquet limit 10;

create table pop_parquet_snappy(
  zip_code int,
  total_population int,
  median_age float,
  total_males int,
  total_females int,
  total_households int,
  average_household_size float
)
stored as parquet
tblproperties('parquet.compress'='SNAPPY');

desc formatted pop_parquet_snappy;

insert into pop_parquet_snappy select * from pop;


select * from pop_parquet_snappy limit 10;

```

### execute in the namenode

``` sh
docker exec -it namenode hdfs dfs -ls /user/hive/warehouse/elnataoliveira.db
docker exec -it namenode hdfs dfs -du /user/hive/warehouse/elnataoliveira.db


```
