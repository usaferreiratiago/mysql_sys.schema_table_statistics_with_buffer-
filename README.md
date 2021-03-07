# mysql_sys.schema_table_statistics_with_buffer-

SELECT 
    `pst`.`OBJECT_SCHEMA` AS `table_schema`,
    `pst`.`OBJECT_NAME` AS `table_name`,
    `pst`.`COUNT_FETCH` AS `rows_fetched`,
    FORMAT_PICO_TIME(`pst`.`SUM_TIMER_FETCH`) AS `fetch_latency`,
    `pst`.`COUNT_INSERT` AS `rows_inserted`,
    FORMAT_PICO_TIME(`pst`.`SUM_TIMER_INSERT`) AS `insert_latency`,
    `pst`.`COUNT_UPDATE` AS `rows_updated`,
    FORMAT_PICO_TIME(`pst`.`SUM_TIMER_UPDATE`) AS `update_latency`,
    `pst`.`COUNT_DELETE` AS `rows_deleted`,
    FORMAT_PICO_TIME(`pst`.`SUM_TIMER_DELETE`) AS `delete_latency`,
    `sys`.`fsbi`.`count_read` AS `io_read_requests`,
    FORMAT_BYTES(`sys`.`fsbi`.`sum_number_of_bytes_read`) AS `io_read`,
    FORMAT_PICO_TIME(`sys`.`fsbi`.`sum_timer_read`) AS `io_read_latency`,
    `sys`.`fsbi`.`count_write` AS `io_write_requests`,
    FORMAT_BYTES(`sys`.`fsbi`.`sum_number_of_bytes_write`) AS `io_write`,
    FORMAT_PICO_TIME(`sys`.`fsbi`.`sum_timer_write`) AS `io_write_latency`,
    `sys`.`fsbi`.`count_misc` AS `io_misc_requests`,
    FORMAT_PICO_TIME(`sys`.`fsbi`.`sum_timer_misc`) AS `io_misc_latency`,
    FORMAT_BYTES(`sys`.`ibp`.`allocated`) AS `innodb_buffer_allocated`,
    FORMAT_BYTES(`sys`.`ibp`.`data`) AS `innodb_buffer_data`,
    FORMAT_BYTES((`sys`.`ibp`.`allocated` - `sys`.`ibp`.`data`)) AS `innodb_buffer_free`,
    `sys`.`ibp`.`pages` AS `innodb_buffer_pages`,
    `sys`.`ibp`.`pages_hashed` AS `innodb_buffer_pages_hashed`,
    `sys`.`ibp`.`pages_old` AS `innodb_buffer_pages_old`,
    `sys`.`ibp`.`rows_cached` AS `innodb_buffer_rows_cached`
FROM
    ((`performance_schema`.`table_io_waits_summary_by_table` `pst`
    LEFT JOIN `sys`.`x$ps_schema_table_statistics_io` `fsbi` ON (((`pst`.`OBJECT_SCHEMA` = `sys`.`fsbi`.`table_schema`)
        AND (`pst`.`OBJECT_NAME` = `sys`.`fsbi`.`table_name`))))
    LEFT JOIN `sys`.`x$innodb_buffer_stats_by_table` `ibp` ON (((`pst`.`OBJECT_SCHEMA` = CONVERT( `sys`.`ibp`.`object_schema` USING UTF8MB4))
        AND (`pst`.`OBJECT_NAME` = CONVERT( `sys`.`ibp`.`object_name` USING UTF8MB4)))))
ORDER BY `pst`.`SUM_TIMER_WAIT` DESC
