# MySQL SELECT文の基本

<!-- omit in toc -->
## はじめに
本書は、MySQLのデータ操作言語（DDL）であるSELECT文の基本を網羅することを目的に記述する。  
SELECT文も含めたMySQLの基本構文の簡易的説明に加えて、実際にA5M2でSQL実行を実践している「[MySQL基本構文実践書](../mysql基本構文実践書/mysql基本構文実践書.md)」も併せて読むことを推奨する。  

<!-- omit in toc -->
## 目次
- [SELECT文の構造](#select文の構造)
  - [SELECT句](#select句)
    - [全てのカラムを抽出する場合](#全てのカラムを抽出する場合)
    - [抽出したカラムに別名をつける場合](#抽出したカラムに別名をつける場合)
  - [WHERE句](#where句)
    - [NULLの扱い](#nullの扱い)
  - [ORDER BY句](#order-by句)
  - [集約関数](#集約関数)
    - [レコード数の集計（COUNT関数）](#レコード数の集計count関数)
    - [数値の集計（SUM関数/AVG関数/MAX関数/MIN関数）](#数値の集計sum関数avg関数max関数min関数)
    - [重複を除いたデータの抽出（DISTINCT）](#重複を除いたデータの抽出distinct)
    - [重複を除いたレコード数の抽出（DISTINCT×COUNT）](#重複を除いたレコード数の抽出distinctcount)
  - [グループ化（GROUP BY句）](#グループ化group-by句)
  - [グループへの抽出条件を指定する（HAVING句）](#グループへの抽出条件を指定するhaving句)
  - [内部結合（INNNER JOIN）](#内部結合innner-join)
  - [外部結合（OUTER JOIN）](#外部結合outer-join)
    - [RIGHT OUTER JOIN](#right-outer-join)
  - [3つ以上のテーブル結合](#3つ以上のテーブル結合)
    - [3つ以上のテーブルの内部結合](#3つ以上のテーブルの内部結合)
    - [3つ以上のテーブルの外部結合](#3つ以上のテーブルの外部結合)

## SELECT文の構造
- SELECT文各句の実行順    
~~~
SELECT ID , NAME ,GENDER , BIRTHDAY , WEIGHT   /* SELECT句  ：2. どのカラムのデータを抽出するか */
  FROM SAMPLE_4_1 ;                            /* FROM句    ：1. どのテーブルから */
~~~

### SELECT句

#### 全てのカラムを抽出する場合
SELECT句で全てのカラムを抽出する場合、"*"を指定する。  
~~~
SELECT *                /* "*"を指定すると、全てのカラムが対象になる */
  FROM SAMPLE_4_1 ;
~~~

#### 抽出したカラムに別名をつける場合
SELECT句でカラム名の後ろに"AS ～"とつけると、～という名前のエイリアスを作ることができる。  
なお、エイリアス名は日本語（"ペットID"など）でもよい。  s
~~~
SELECT  ID       AS PET_ID       ,      /* 物理名"ID"       → エイリアス"PET_ID"         */
        NAME     AS PET_NAME     ,      /* 物理名"NAME"     → エイリアス"PET_NAME"       */
        GENDER   AS PET_GENDER   ,      /* 物理名"GENDER"   → エイリアス"PET_GENDER"     */
        BIRTHDAY AS PET_BIRTHDAY ,      /* 物理名"BIRTHDAY" → エイリアス"PET_BIRTHDAY"   */
        WEIGHT   AS PET_WEIGHT          /* 物理名"WEIGHT"   → エイリアス"PET_WEIGHT"     */
 FROM SAMPLE_4_1 ;
~~~

### WHERE句
WHWRE句では、SELECT句で取得するデータの対象行を指定するための条件式を記述する。条件式は下記に示す論理演算子を組み合わせて作成する。  
- SELECT文中での実行順    
~~~
SELECT ID , NAME ,GENDER , BIRTHDAY , WEIGHT        /* 3. どのカラムのデータを抽出するか */
 FROM SAMPLE_4_1                                    /* 1. どのテーブルから */
 WHERE GENDER = 'M’                                 /* 2. どの行の */
 AND WEIGHT > 6.2
~~~
- WHWRE句で扱う演算子一覧  

|演算子  |説明  |
|---|---|
|=  |左辺と右辺が等しければ真  |
|!=<br><>  |左辺と右辺が等しくなければ真  |
|>  |左辺が右辺よりも大きければ真  |
|>=  |左辺が右辺以上であれば真  |
|<  |左辺が右辺よりも小さければ真  |
|<=  |左辺が右辺以下であれば真  |
|AND |2つの条件を結合し、両方とも真であれば真 |
|OR |2つの条件を結合し、どちらか一方でも真であれば真 |
|NOT |結果を反転（真であれば偽、偽であれば真） |
|BETWEEN 値a AND 値b |値a以上、値b以下の範囲内であれば真 |
|IN |対象のフィールドが（）内の値のいずれかと一致すれば真 |
|LIKE |対象のフィールドがパターンに一致すれば真 |

- 論理演算子の優先順位：NOT > AND > OR  

- WHWRE句SQL文例  
~~~
SELECT *
  FROM SAMPLE_4_1
 WHERE NAME IN ('MOCO','TARO','RINRIN') ; /* カラム「NAME」の値が'MOCO'.'TARO','RINRIN'のいずれかであれば抽出する。 */
~~~
~~~
SELECT *
  FROM SAMPLE_4_1
 WHERE NAME NOT IN ('MOCO','TARO','RINRIN') ; /* カラム「NAME」の値が'MOCO'.'TARO','RINRIN'のいずれにも該当しなければ抽出する。 */
~~~
~~~
SELECT *
  FROM SAMPLE_4_1
 WHERE BIRTHDAY LIKE '2013%' ; /* カラム「BIRTHDAY」の値が'2013'から始まっていれば抽出する。 */
~~~
~~~
SELECT *
  FROM SAMPLE_4_1
 WHERE WEIGHT BETWEEN 5.0 AND 8.0 ; /* カラム「WEIGHT」の値が5.0以上8.0以下であれば抽出する。 */
~~~

#### NULLの扱い
比較演算子でNULLを比較しようとすると、思わぬエラーになることがある。  
（NULLと比較するとほとんどの場合、結果がTRUEでもFALSEでもなくNULL（不明）になってしまう）  
→**IS NULL**、**IS NOT NULL**を用いる。  
~~~
SELECT *
 FROM SAMPLE_4_2
 WHERE PET_ID IS NULL ; /* PET_IDがNULLであれば抽出する。 */
~~~
~~~
SELECT *
 FROM SAMPLE_4_2
 WHERE PET_ID IS NOT NULL ; /* PET_IDがNULLでなければ抽出する。 */
~~~

### ORDER BY句
ORDER BY句では、抽出したデータをどう並び替えて表示させるかを指定する。
- SELECT文中での実行順    
~~~
 SELECT ID , NAME ,GENDER , BIRTHDAY , WEIGHT   /* 3. どのカラムのデータを抽出して */
 FROM SAMPLE_4_1                                /* 1. どのテーブルから */
 WHERE GENDER = 'M’                             /* 2. どの行の */
 ORDER BY BIRTHDAY , WEIGHT DESC                /* 4. どう並び替えて表示させるか */
~~~

- ORDER BY の後ろに、優先順位順にカラムを指定する。カラム名の後ろに'DESC'をつけると降順になり、何もつけなければ昇順になる。
~~~
 ORDER BY BIRTHDAY , WEIGHT DESC   /* 優先順位1：BIRTHDAY（昇順）、優先順位2：WEIGHT（降順） */
~~~

### 集約関数
SQLでもExcelと同じように集約関数が存在する。  

|関数名  |機能  |
|---|---|
|COUNT |レコード数を求める |
|SUM |数値データ列の合計値を求める |
|AVG |数値データ列の平均値を求める |
|MAX |数値データ列の最大値を求める |
|MIN |数値データ列の最小値を求める |

#### レコード数の集計（COUNT関数）
~~~
SELECT COUNT(*) , COUNT(PET_ID)   /* 2. 全レコード数と、PET_IDのデータ数(NULLを除く)を抽出する。 */
  FROM SAMPLE_4_2 ;               /* 1. SAMPLE_4_2テーブルの、 */
~~~

#### 数値の集計（SUM関数/AVG関数/MAX関数/MIN関数）
~~~
SELECT SUM(WEIGHT) ,  /* 2. WEIGHTの合計値と、                */
 AVG(WEIGHT) ,        /*    WEIGHTの平均値と、                */
 MAX(WEIGHT) ,        /*    WEIGHTの最大値と、                */
 MIN(WEIGHT)          /*    WEIGHTの最小値を抽出して取得する。  */
 FROM SAMPLE_4_1 ;    /* 1. SAMPLE_4_1テーブルの、 */
~~~

#### 重複を除いたデータの抽出（DISTINCT）
~~~
SELECT DISTINCT GENDER  /* GENDERの値を重複を除いて取得する。 */
  FROM SAMPLE_4_1 ;     /* SAMPLE4_1テーブルの、 */
~~~

#### 重複を除いたレコード数の抽出（DISTINCT×COUNT）
~~~
SELECT COUNT( DISTINCT GENDER )  /* GENDERの値の候補値の数を取得する。 */
  FROM SAMPLE_4_1 ;              /* SAMPLE4_1テーブルの、 */
~~~

### グループ化（GROUP BY句）
- SELECT文中での実行順  
~~~
SELECT SCHOOL_NAME AS SCHOOL_NAME   ,                           /* 4. どのカラムのデータを抽出して */
       MAX(SCORE)  AS HIGH_SCORE    ,
       AVG(SCORE)  AS AVERAGE_SCORE
  FROM SAMPLE_4_4                                               /* 1. どのテーブルの */
 WHERE SCHOOL_NAME IS NOT NULL                                  /* 2. どの行を */
 GROUP BY SCHOOL_NAME                                           /* 3. どの集約キーでグループ化してデータ表を作り、 */
 ORDER BY HIGH_SCORE DESC , AVERAGE_SCORE DESC , SCHOOL_NAME ;  /* 5. どう並び替えて表示させるか */
~~~
上の例の場合、SCHOOL_NAMEの値ごとにレコードをまとめて（SCHOOL_NAME別のデータ表を作って）それぞれの表で、MAX(SCORE),AVG(SCORE)を計算し、結果を出力する。  
参考までに出力結果のイメージを以下に示す。 
- 元の表（SAMPLE_4_4）　　

|ID |NAME |SCHOOLE_NAME |SCORE |
|---|---|---|---|
|1 |MOCO |SCHOOL_A |90 |
|2 |PECO |SCHOOL_B |90 |
|3 |MIKA |SCHOOL_A |92 |
|4 |POCHA |SCHOOL_C |90 |
|~ |~ |~ |~ |
|~ |~ |~ |~ |
|~ |~ |~ |~ |
  
- SQL文の出力結果  

|SCHOOL_NAME |HIGH_SCORE | AVERAGE_SCORE |
|---|---|---|
|SCHOOL_A |92 | 74 |
|SCHOOL_B |90 | 76 |
|SCHOOL_C |90 | 68 |

### グループへの抽出条件を指定する（HAVING句）
HAVING句では、「GROUOP BY」でグループ化された後の集計結果に対して、抽出条件を指定することができる。  
WHERE句との違いは以下の通り。  

|比較項目 |WHWRE句 |HAVING句 |
|---|---|---|
|適用タイミング |グループ化前 |グループ化後 |
|使用可能な条件 | 個々の行に対する条件 |集計関数（COUNT,SUM,AVGなど）を使った条件 |
|例 |WHWRE salary > 50000 |HAVING AVG(salary) > 50000 |

- SELECT文中での実行順  
~~~
SELECT SCHOOL_NAME AS SCHOOL_NAME   ,                           /* 5. どのカラムのデータを抽出して  
       MAX(SCORE)  AS HIGH_SCORE    ,                           
       AVG(SCORE)  AS AVERAGE_SCORE
  FROM SAMPLE_4_4                                               /* 1. どのテーブルの */
 WHERE SCHOOL_NAME IS NOT NULL                                  /* 2. どの行を */
 GROUP BY SCHOOL_NAME                                           /* 3. どの集約キーでグループ化して データ表を作り、 */
HAVING COUNT(*) >= 3                                            /* 4. どの行の、
 ORDER BY HIGH_SCORE DESC , AVERAGE_SCORE DESC , SCHOOL_NAME ;  /* 6. どう並び替えて表示させるか */
~~~

### 内部結合（INNNER JOIN）
内部結合を用いることで、複数のテーブルの共通する値をもとに、対応する行だけを結合して表示することができる。  
例えば、ペットの情報を管理する「SAMPLE_4_1」と家庭の情報を管理する「SAMPLE_4_2」を内部結合すると以下のようになる。
また、非推奨ではあるが、INNNER JOIN句を使わずにWHWRE句で実装することもできる。  

- SAMPLE4_1  

|ID |NAME |GENDER |BIRTHDAY |WEIGHT |REGIST_TIMESTAMP |
|---|-----|-------|---------|-------|-----------------|
|1  |MOCO |F      |2014/05/04|3.5   |2018/07/09 4:35:36|
|2  |CHOKO|M      |2011/08/25|5.2   |2018/07/09 4:35:36|
|3  |TARO |M      |2013/01/02|7.9   |2018/07/09 4:35:36|
|4  |RINRIN|F     |2015/12/12|6.2   |2018/07/09 4:35:36|
|5  |CHAMP|M      |2013/01/02|10.9  |2018/07/09 4:35:36|

- SAMPLE4_2  

|HOME_ID |HOME_NAME   |PET_ID |AREA_ID |
|--------|------------|-------|--------|
|1       |OKAMOTO_KE  |1      |4       |
|2       |TANAKA_KE   |NULL   |NULL    |
|3       |SUZUKI_KE   |5      |3       |
|4       |IKEDA_KE    |NULL   |4       |
|5       |TAKAHASHI_KE|2      |1       |
|6       |NAGASAWA_KE |3      |NULL    |
|7       |TAKIMIZU_KE |4      |2       |

SQL-INNNER_JOINを用いる場合（エイリアス名 a,bは省略可能）
~~~
SELECT b.HOME_ID , b.HOME_NAME , a.NAME                         /* 2. SAMPLE_4_2のHOME_ID,HOME_NAME、SAMPLE_4_1のPET_IDのカラムのデータを抽出して */
  FROM SAMPLE_4_1 a INNER JOIN SAMPLE_4_2 b ON b.PET_ID = a.ID  /* 1. SAMPLE_4_1のIDとSAMPLE_4_2のPET_IDを結合キーとして内部結合したテーブルの */
 ORDER BY b.HOME_ID ;                                           /* 3. HOME_IDの昇順に並び替えて表示する。 */
~~~

SQL-WHEREを用いる場合（非推奨）（エイリアス名 a,bは省略可能）
~~~
SELECT b.HOME_ID , b.HOME_NAME , a.NAME   /* 3. SAMPLE_4_2のHOME_ID,HOME_NAME、SAMPLE_4_1のPET_IDのカラムのデータを抽出して */
  FROM SAMPLE_4_1 a ,                     /* 1. SAMPLE4_1とSAMPLE4_2のテーブルの */
       SAMPLE_4_2 b
 WHERE b.PET_ID  = a.ID                   /* 2. SAMPLE4_2のPET_IDとSAMPLE4_1のIDが一致する行を */
 ORDER BY b.HOME_ID ;                     /* 4. HOME_IDの昇順に並び替えて表示する。 */
~~~

- SQL実行後（内部結合後）

|HOME_ID |HOME_NAME   |NAME   |
|--------|------------|-------|
|1       |OKAMOTO_KE  |MOCO   |
|3       |SUZUKI_KE   |CHAMP  |
|5       |TAKAHASHI_KE|CHOKO  |
|6       |NAGASAWA_KE |TARO   |
|7       |TAKIMIZU_KE |RINRIN |


### 外部結合（OUTER JOIN）
外部結合は、結合条件に一致しない行も含めて結果を返す方法である。
主に以下の3種類がある。

#### RIGHT OUTER JOIN
RIGHT OUTER JOINでは、"OUTER JOIN"文の右側にあるテーブルを軸（駆動テーブル）として結合を行う。  
駆動テーブルのテーブルはすべての行が返される。  

~~~
SELECT b.HOME_ID , b.HOME_NAME , a.NAME
  FROM SAMPLE_4_1 a RIGHT OUTER JOIN SAMPLE_4_2 b ON b.PET_ID = a.ID
 ORDER BY b.HOME_ID ;
~~~

- SAMPLE4_2を駆動テーブル（軸）として、SAMPLE4_1と結合する
- 結合キーはSAMPLE4_2の"PET_ID"とSAMPLE4_1の"ID"
- *LEFT OUTER JOIN*の場合は、左側のテーブルが駆動テーブルとなる

### 3つ以上のテーブル結合
3つ以上のテーブルのを結合は、INNNER JOIN/OUTER JOIN構文を複数回実行し、2つのテーブルを結合したテーブルにさらに別のテーブルを結合する、という手法で実現できる。

#### 3つ以上のテーブルの内部結合
~~~
SELECT b.HOME_ID , b.HOME_NAME , a.NAME , c.AREA_NAME
  FROM ( SAMPLE_4_1 a INNER JOIN SAMPLE_4_2 b ON b.PET_ID  = a.ID )
                      INNER JOIN SAMPLE_4_3 c ON b.AREA_ID = c.AREA_ID
~~~
1. SAMPLE4_1の"ID"とSAMPLE4_2の"PET_ID"を結合キーとして内部結合をする ←結合テーブル１
2. 結合テーブル１の"AREA_ID"（元々はSAMPLE4_2の"AREA_ID"）とSAMPLE4_3の"AREA_ID"を結合キーとして内部結合する

#### 3つ以上のテーブルの外部結合
~~~
SELECT b.HOME_ID , b.HOME_NAME , a.NAME , c.AREA_NAME
  FROM ( SAMPLE_4_1 a RIGHT OUTER JOIN SAMPLE_4_2 b ON b.PET_ID  = a.ID )
                      LEFT  OUTER JOIN SAMPLE_4_3 c ON b.AREA_ID = c.AREA_ID
 ORDER BY b.HOME_ID 
~~~
1. SAMPLE_4_2を駆動テーブルとしてSAMPLE4_1と結合。結合キーはSAMPLE4_2の"PET_ID"とSAMPLE4_1の"ID"。　←結合テーブル１
2. 結合テーブル１を駆動テーブルとしてSAMPLE_4_3と結合。結合キーは結合テーブル１の"AREA_ID"（元々はSAMPLE4_2の"AREA_ID"）とSAMPLE_4_3の"AREA_ID"。


以上