### db设计

* 接入系统权限表：app_secret
```
CREATE TABLE `app_secret` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `app` varchar(200) DEFAULT NULL COMMENT 'app名称(根据此生成secret)',
  `secret` varchar(200) DEFAULT NULL COMMENT '生成的secret',
  `status` int(1) DEFAULT NULL COMMENT '状态0无效1有效',
  `operator_ip` varchar(100) DEFAULT NULL COMMENT '添加者IP',
  `operator_time` datetime DEFAULT NULL COMMENT '添加时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
* 访问日志记录表：access_logs
```
CREATE TABLE `access_logs` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `access_ip` varchar(100) DEFAULT NULL COMMENT '访问IP',
  `card_id` int(200) DEFAULT NULL COMMENT '卡片ID',
  `card_type` int(20) DEFAULT NULL COMMENT '卡片类型',
  `push_type` int(1) DEFAULT NULL COMMENT '0手动1自动',
  `app_secret` varchar(100) DEFAULT NULL COMMENT 'app密钥',
  `time` int(100) DEFAULT NULL COMMENT '时间戳',
  `sign` varchar(1000) DEFAULT NULL COMMENT 'sign值',
  `operator_id` int(100) DEFAULT NULL COMMENT '操作人ID',
  `operator_name` varchar(200) DEFAULT NULL COMMENT '操作人',
  `operator_time` datetime DEFAULT NULL COMMENT '操作时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
* 消息推送成功历史表：cards_push_history
```
CREATE TABLE `cards_push_history` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `card_id` int(20) DEFAULT NULL COMMENT '卡片ID',
  `card_type` int(20) DEFAULT NULL COMMENT '卡片类型',
  `card_name` varchar(200) COLLATE utf8_bin DEFAULT NULL COMMENT '卡片名称',
  `push_type` tinyint(4) DEFAULT NULL COMMENT '推送类型',
  `push_time` datetime DEFAULT NULL COMMENT '推送时间',
  `push_ip` varchar(200) COLLATE utf8_bin DEFAULT NULL COMMENT '推送者IP',
  `app_secret` varchar(200) COLLATE utf8_bin DEFAULT NULL COMMENT 'app secret',
  `push_sign` varchar(200) COLLATE utf8_bin DEFAULT NULL COMMENT 'sign值',
  `target_profile` text COLLATE utf8_bin COMMENT '目标群体属性，JSON格式数据',
  `status` tinyint(4) DEFAULT NULL COMMENT '推送状态，0：未执行；1：已成功执行；-1：已执行，未成功',
  `operator_id` int(20) DEFAULT NULL COMMENT '操作人ID',
  `operator_name` varchar(200) COLLATE utf8_bin DEFAULT NULL COMMENT '操作人名称',
  `operator_time` datetime DEFAULT NULL COMMENT '操作时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin;
```
