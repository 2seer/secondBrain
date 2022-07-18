```Mysql
-- -----------------------------------------------------

-- PROCEDURE usp_op_get_list_order_v3

-- -----------------------------------------------------

USE billing;
DELIMITER $$

  

DROP PROCEDURE IF EXISTS `usp_op_get_list_order_v3`$$

  

CREATE DEFINER=CURRENT_USER() PROCEDURE `usp_op_get_list_order_v3`(

      **IN** pi_vch_service_id **varchar**(30)        -- 서비스 구분 / enum(all, ddks, hodoo_abc, betia, hodoo_english, hodoo_english_m) / 전체, 땅콩스쿨, 호두ABC, 베티아, 호두잉글리시, 호두잉글리시M

    , **IN** pi_vch_search_type **varchar**(30)       -- 검색 유형 / enum(all, order_key, payment_transaction_key, product_name, product_key, account) / 전체, 주문번호, 결제기관 주문번호, 상품 이름, 상품 식별자, 계정

    , **IN** pi_vch_keyword **varchar**(384)          -- 검색 유형이 order_key, payment_transaction_key, product_name인 경우 검색어

    , **IN** pi_jsn_account_ids json              -- 검색 유형이 account인 경우 account_id Array / [account_id, ...]

    , **IN** pi_dat_search_start_date **date**        -- 검색 유형이 all, product_name, product_key인 경우 검색 시작 일자

    , **IN** pi_dat_search_end_date **date**          -- 검색 유형이 all, product_name, product_key인 경우 검색 종료 일자

    , **IN** pi_chr_is_shipment_required **char**(1)  -- 배송 상품 유무(y, n) / 전체는 NULL

    , **IN** pi_int_page **int** **UNSIGNED**             -- 페이지

    , **IN** pi_int_page_size **int** **UNSIGNED**        -- 페이지 크기

    , **IN** pi_dt5_now **datetime**(0)               -- 현재 서버 시각

    , **OUT** po_int_return **int**                   -- 리턴 값

)

**NOT** **DETERMINISTIC**

**SQL** **SECURITY** **DEFINER**

**CONTAINS** **SQL**

COMMENT '

author : jiseong.lee

e-mail : jiseong.lee@hodoolabs.com

created date : 2022-07-08

description : 주문 이력을 조회합니다.

parameter :

      IN pi_vch_service_id varchar(30)        -- 서비스 구분 / enum(all, ddks, hodoo_abc, betia, hodoo_english, hodoo_english_m) / 전체, 땅콩스쿨, 호두ABC, 베티아, 호두잉글리시, 호두잉글리시M

    , IN pi_vch_search_type varchar(30)       -- 검색 유형 / enum(all, order_key, payment_transaction_key, product_name, product_key, account) / 전체, 주문번호, 결제기관 주문번호, 상품 이름, 상품 식별자, 계정

    , IN pi_vch_keyword varchar(384)          -- 검색 유형이 order_key, payment_transaction_key, product_name인 경우 검색어

    , IN pi_jsn_account_ids json              -- 검색 유형이 account인 경우 account_id Array / [account_id, ...]

    , IN pi_dat_search_start_date date        -- 검색 유형이 all, product_name, product_key인 경우 검색 시작 일자

    , IN pi_dat_search_end_date date          -- 검색 유형이 all, product_name, product_key인 경우 검색 종료 일자

    , IN pi_chr_is_shipment_required char(1)  -- 배송 상품 유무(y, n) / 전체는 NULL

    , IN pi_int_page int UNSIGNED             -- 페이지

    , IN pi_int_page_size int UNSIGNED        -- 페이지 크기

    , IN pi_dt5_now datetime(0)               -- 현재 서버 시각

    , OUT po_int_return int                   -- 리턴 값

result set :

    order_id / int UNSIGNED / 주문 ID

    account_id / int UNSIGNED / 계정 ID

    family_id / varchar(10) / 패밀리 스토어 결제인 경우, 패밀리 계정 ID

    receiver_name / varchar(50) / 패밀리 스토어 결제인 경우, 수신자 이름

    receiver_phone_number / varchar(15) / 패밀리 스토어 결제인 경우, 수신자 휴대폰 번호

    is_package / varchar(1) / 패키지 유무 (n:패키지 아님, y:패키지)

    order_key / varchar(64) / 주문번호

    payment_transaction_key / varchar(384) / 결제기관 주문번호

    payment_type / enum(pg, in_app, point_only, iamport) / 결제 유형 - PG, 인앱, 포인트, 아임포트

    pg_id / enum(payapp, naverpay, payco, kakaopay, danal) / 결제 유형이 pg 또는 iamport인 경우, PG사

    store_id / enum(play_store, app_store, one_store) / 결제 유형이 in_app인 경우 스토어 - 플레이 스토어, 앱 스토어, 원 스토어

    service_id / enum(ddks, hodoo_abc, betia, hodoo_english, hodoo_english_m, package) / 서비스 구분 - 땅콩스쿨, 호두ABC, 베티아, 호두잉글리시, 호두잉글리시M, 패키지상품

    product_id / mediumint UNSIGNED / 상품 ID

    product_name / mediumint UNSIGNED / 상품 ID

    product_key / varchar(64) / 상품 식별자

    amount / decimal(10,2) / 결제 금액

    currency_code / char(3) / 통화 코드 - ISO 4217

    outgo_point / int UNSIGNED / 사용한 포인트

    is_recurrent_payment / enum(y,n) / 반복 결제 여부

    order_complete_date / datetime(0) / 결제 일자

    refund_status / enum(requested, return_ongoing, return_completed, pg_ongoing, pg_succeeded, pg_failed, completed) / 환불 상태 - 요청, 반품 진행, 반품 완료, PG사 환불 진행, PG사 환불 성공, PG사 환불 실패 (최대 재시도 횟수 초과), 환불 및 회수 완료

    shipment_required  / enum(y,n) / 배송 상품 유무

    shipment_tracking_number / bigint UNSIGEND / 운송장 번호

    courier_id / tinyint UNSIGNED / 택배사 ID

return value :

     0 = 에러가 없습니다.

    -1 = 예상하지 않은 런 타임 오류가 발생하였습니다.

commit message :

    family_id, receiver_name, receiver_phone_number 추가

/* ##ssp_debug_00## */

'

proc_body: **BEGIN**

    **DECLARE** v_vch_proc_name **varchar**(100) **DEFAULT** 'usp_op_get_list_order_v3';

    **DECLARE** v_iny_proc_step **tinyint** **UNSIGNED** **DEFAULT** 0;

    **DECLARE** v_txt_call_stack **mediumtext**;

    **DECLARE** v_bit_write_exception_log **boolean** **DEFAULT** **FALSE**;

    **DECLARE** v_vch_sql_state **varchar**(5);

    **DECLARE** v_int_error_no **int**;

    **DECLARE** v_txt_error_msg **text**;

  

    **DECLARE** v_int_offset **int** **UNSIGNED** **DEFAULT** (pi_int_page - 1) * pi_int_page_size;

  

    **DECLARE** **CONTINUE** HANDLER **FOR** **NOT** **FOUND** **BEGIN** **END**;

    **DECLARE** **CONTINUE** HANDLER **FOR** **SQLWARNING** **BEGIN** **END**;

  

    **DECLARE** **EXIT** HANDLER **FOR** **SQLEXCEPTION**

    **BEGIN**

        **GET** **DIAGNOSTICS** **CONDITION** 1 v_vch_sql_state = RETURNED_SQLSTATE

            , v_int_error_no = MYSQL_ERRNO

            , v_txt_error_msg = MESSAGE_TEXT;

  

        **ROLLBACK**;

/* ##ssp_debug_01## */

        -- 런 타임 에러 중 논리 에러로 분류할 항목을 필터링

        **IF** v_bit_write_exception_log **THEN**  -- exception_log table에 기록하는 논리 에러인 경우

            **INSERT** exception_log (proc_name, proc_step, exception_no, call_stack, proc_call_date, log_date)

            **VALUES** (v_vch_proc_name, v_iny_proc_step, po_int_return, v_txt_call_stack, pi_dt5_now, **NOW**(0));

  

        **ELSEIF** po_int_return = 0 **OR** po_int_return **IS** **NULL** **THEN**  -- exception으로 분류하지 않는 그 밖의 런 타임 에러인 경우

            **INSERT** error_log (proc_name, proc_step, sql_state, error_no, error_msg, call_stack, proc_call_date, log_date)

            **VALUES** (v_vch_proc_name, v_iny_proc_step, v_vch_sql_state, v_int_error_no, v_txt_error_msg, v_txt_call_stack, pi_dt5_now, **NOW**(0));

  

            **SET** po_int_return = -1;

            **RESIGNAL**;

        **END** **IF**;

    **END**;

/* ##ssp_debug_02## */

    **IF** pi_vch_search_type = 'all' **THEN**

        -- 검색 유형이 all 인 경우

        **SET** v_iny_proc_step = 1;

  

        **WITH** ppp **AS** (

            **SELECT**

              product_id

              , service_id

              , product_name

              , product_key

              , 'n' is_package

              , shipment_required

            **FROM** products

            **WHERE** shipment_required = **IFNULL**(pi_chr_is_shipment_required, shipment_required)

            **UNION** **ALL**

            **SELECT**

              product_package_id

              , 'package'

              , product_package_name

              , product_package_key

              , 'y'

              , shipment_required

            **FROM** product_packages

            **WHERE** shipment_required = **IFNULL**(pi_chr_is_shipment_required, shipment_required)

        )

        SELECT STRAIGHT_JOIN
              o.order_id
            , o.account_id
            , o.family_id
            , o.receiver_name

            , o.receiver_phone_number

            , p.is_package

            , o.order_key

            , o.payment_transaction_key

            , o.payment_type

            , o.pg_id

            , o.store_id

            , p.service_id

            , o.product_id

            , p.product_name

            , p.product_key

            , o.amount

            , o.currency_code

            , o.outgo_point

            , o.is_recurrent_payment

            , o.order_complete_date

            , r.refund_status

            , p.shipment_required 

            , s.shipment_tracking_number

            , s.courier_id

        **FROM** orders o **FORCE** **INDEX** **FOR** **JOIN** (`nix-orders-order_complete_date-order_status`)

            **INNER** **JOIN** ppp p **ON** ( p.is_package = 'n' **AND** p.product_id = o.product_id) **OR** ( p.is_package = 'y' **AND** p.product_id = o.product_package_id)

            **LEFT** **JOIN** shipments s **FORCE** **INDEX** **FOR** **JOIN** (**PRIMARY**) **ON** s.order_id = o.order_id

            **LEFT** **OUTER** **JOIN** refunds r **FORCE** **INDEX** **FOR** **JOIN** (**PRIMARY**) **ON** o.order_id = r.order_id

        **WHERE** o.order_complete_date >= **CAST**(pi_dat_search_start_date **AS** **datetime**)

            **AND** o.order_complete_date < TIMESTAMPADD(**DAY**, 1, **CAST**(pi_dat_search_end_date **AS** **datetime**))

            **AND** o.order_status = 'completed'

            **AND** o.is_test = 'n'

            **AND** **CASE** **WHEN** pi_chr_is_shipment_required = 'y' **THEN**

            -- 3개 내에 deperecated 시킬 서비스 항목(7월)

                p.service_id <> 'ddks'

              **ELSE**

                p.service_id = **IFNULL**(pi_vch_service_id, p.service_id)

              **END**

        **ORDER** **BY** o.order_complete_date **DESC**

        **LIMIT** v_int_offset, pi_int_page_size;

    **ELSE**

        **SET** @stmt = '

WITH ppp AS (

            SELECT

              product_id

              , service_id

              , product_name

              , product_key

              , ''n'' is_package

              , shipment_required

            FROM products

            WHERE shipment_required = IFNULL(pi_chr_is_shipment_required, shipment_required)

            UNION ALL

            SELECT

              product_package_id

              , ''package''

              , product_package_name

              , product_package_key

              , ''y'' is_package

              , shipment_required

            FROM product_packages

            WHERE shipment_required = IFNULL(pi_chr_is_shipment_required, shipment_required)

        )

SELECT STRAIGHT_JOIN

      o.order_id

    , o.account_id

    , o.family_id

    , o.receiver_name

    , o.receiver_phone_number

    , p.is_package

    , o.order_key

    , o.payment_transaction_key

    , o.payment_type

    , o.pg_id

    , o.store_id

    , p.service_id

    , o.product_id

    , p.product_name

    , p.product_key

    , o.amount

    , o.currency_code

    , o.outgo_point

    , o.is_recurrent_payment

    , o.order_complete_date

    , r.refund_status

    , p.shipment_required 

    , s.shipment_tracking_number

    , s.courier_id';

  

        **IF** pi_vch_search_type **IN** ('order_key', 'payment_transaction_key') **THEN** -- enum(all, order_key, payment_transaction_key, product_name, product_key, account)

            **SET** v_iny_proc_step = 2;

            **SET** @stmt = **CONCAT**(@stmt, 'FROM orders o FORCE INDEX FOR JOIN (`'

                , **CASE** pi_vch_search_type

                    **WHEN** 'order_key' **THEN** 'uix-orders-order_key'

                    **WHEN** 'payment_transaction_key' **THEN** 'uix-orders-payment_transaction_key'

                **END**

                , '`)

    INNER JOIN ppp p ON (p.is_package = ''n'' AND p.product_id = o.product_id) OR (p.is_package = ''y'' AND p.product_id = o.product_package_id)

    LEFT JOIN shipments s FORCE INDEX FOR JOIN (PRIMARY) ON s.order_id = o.order_id

    LEFT OUTER JOIN refunds r FORCE INDEX FOR JOIN (PRIMARY) ON o.order_id = r.order_id

WHERE o.'

                , **CASE** pi_vch_search_type

                    **WHEN** 'order_key' **THEN** 'order_key'

                    **WHEN** 'payment_transaction_key' **THEN** 'payment_transaction_key'

                **END**

            , ' = ?

    AND o.order_status = ''completed''

    AND o.is_test = ''n'';');

            **PREPARE** stmt **FROM** @stmt;

            **SET** @keyword = pi_vch_keyword;

            **EXECUTE** stmt **USING** @keyword;

  

        **ELSEIF** pi_vch_search_type **IN** ('product_name', 'product_key') **THEN**

            **SET** v_iny_proc_step = 3;

            **SET** @stmt = **CONCAT**(@stmt, '

FROM ppp p'

                , **CASE** pi_vch_search_type

                    **WHEN** 'product_name' **THEN** ''

                    **WHEN** 'product_key' **THEN** ''

                **END**

                , '

    INNER JOIN orders o ON (p.is_package = ''n'' AND p.product_id = o.product_id) OR (p.is_package = ''y'' AND p.product_id = o.product_package_id)

    LEFT JOIN shipments s FORCE INDEX FOR JOIN (PRIMARY) ON s.order_id = o.order_idd

    LEFT OUTER JOIN refunds r FORCE INDEX FOR JOIN (PRIMARY) ON o.order_id = r.order_id

WHERE '

                , **CASE** pi_vch_search_type

                    **WHEN** 'product_name' **THEN** 'p.product_name LIKE '

                    **WHEN** 'product_key' **THEN** 'p.product_key = '

                **END**

                , '?

    AND o.order_complete_date >= ?

    AND o.order_complete_date < ?

    AND o.order_status = ''completed''

    AND o.is_test = ''n''

ORDER BY o.order_complete_date DESC

LIMIT ', v_int_offset, ', ', pi_int_page_size, ';');

  

            **PREPARE** stmt **FROM** @stmt;

            **SET** @keyword = 

                **CASE** pi_vch_search_type

                    **WHEN** 'product_name' **THEN** **CONCAT**('%', pi_vch_keyword, '%')

                    **WHEN** 'product_key' **THEN** pi_vch_keyword

                **END**;

            **SET** @start_date = **CAST**(pi_dat_search_start_date **AS** **datetime**);

            **SET** @end_date = TIMESTAMPADD(**DAY**, 1, **CAST**(pi_dat_search_end_date **AS** **datetime**));

  

            **EXECUTE** stmt **USING** @keyword, @start_date, @end_date;

  

        **ELSEIF** pi_vch_search_type **IN** ('account') **THEN**

            **SET** v_iny_proc_step = 4;

  

            **WITH** a **AS** (

                **SELECT** account_id

                **FROM** **JSON_TABLE**(

                      pi_jsn_account_ids

                    , '$[*]' **COLUMNS** (

                        account_id **int** **UNSIGNED** **PATH** '$'

                    )

                ) jt

            )

            , ppp **AS** (

                **SELECT**

                  product_id

                  , service_id

                  , product_name

                  , product_key

                  , 'n' is_package

                  , shipment_required

                **FROM** products

                **WHERE** shipment_required = **IFNULL**(pi_chr_is_shipment_required, shipment_required)

                **UNION** **ALL**

                **SELECT**

                  product_package_id

                  , 'package'

                  , product_package_name

                  , product_package_key

                  , 'y' is_package

                  , shipment_required

                **FROM** product_packages

                **WHERE** shipment_required = **IFNULL**(pi_chr_is_shipment_required, shipment_required)

            )

            **SELECT** **STRAIGHT_JOIN**

                  o.order_id

                , o.account_id

                , o.family_id

                , o.receiver_name

                , o.receiver_phone_number

                , p.is_package

                , o.order_key

                , o.payment_transaction_key

                , o.payment_type

                , o.pg_id

                , o.store_id

                , p.service_id

                , o.product_id

                , p.product_name

                , p.product_key

                , o.amount

                , o.currency_code

                , o.outgo_point

                , o.is_recurrent_payment

                , o.order_complete_date

                , r.refund_status

                , p.shipment_required 

                , s.shipment_tracking_number

                , s.courier_id

            **FROM** a

                **INNER** **JOIN** orders o **FORCE** **INDEX** **FOR** **JOIN** (`nix-orders-account_id-order_status-order_complete_date`) **ON** o.account_id = a.account_id

                **INNER** **JOIN** ppp p **ON** ( p.is_package = 'n' **AND** p.product_id = o.product_id) **OR** ( p.is_package = 'y' **AND** p.product_id = o.product_package_id)

                **LEFT** **JOIN** shipments s **FORCE** **INDEX** **FOR** **JOIN** (**PRIMARY**) **ON** s.order_id = o.order_id

                **LEFT** **OUTER** **JOIN** refunds r  **FORCE** **INDEX** **FOR** **JOIN** (**PRIMARY**) **ON** o.order_id = r.order_id

            **WHERE** o.order_status = 'completed'

                **AND** o.is_test = 'n'

                **AND** **CASE** **WHEN** pi_chr_is_shipment_required = 'y' **THEN**

                  p.service_id <> 'ddks'

                **ELSE**

                  p.service_id = **IFNULL**(pi_vch_service_id, p.service_id)

                **END**

            **ORDER** **BY** o.order_id **DESC**

            **LIMIT** v_int_offset, pi_int_page_size;

        **END** **IF**;

    **END** **IF**;

  

    **SET** po_int_return = 0;

**END** proc_body$$

  

**DELIMITER ;**
```
