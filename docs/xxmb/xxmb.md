
### <span id="top">详细设计
> 现有的卡片类型：

* 投票卡片 [消息模板](#vote)
* 通用卡片 [消息模板](#general)
* 竞猜答题 [消息模板](#quiz)
* 节目导视 [消息模板](#column_guide)
* 礼品商城卡片 [消息模板](#points_mall)
* 预约卡片 [消息模板](#subscribe)
* 暴风卡片 [消息模板](#storm)
* 模块化卡片 [消息模板](#component) 不确认是否在用，抢购卡片有关联？
* 主动推荐卡片 此卡片很丑陋,消息格式好乱 [消息模板](#recommend)
* 广告卡片 遗留卡片，上线后未使用，不知道为啥 [消息模板](#advertise)
* [product.ftl](#product)
* 卡片类型对应ID值表格 [对应表](#typeids)


* 投票卡片消息格式 [返回顶部](#top)
<span id = "vote">
```
	<#import "common.ftl" as global/>
	<#if push_sub_type == 1>
		<#assign cmd ="get_options"/>
	</#if>
	<#if push_sub_type == 2>
		<#assign cmd ="get_result"/>
	</#if>
	{
	    "id": "${card.id?c}",
	    "type": 16,
	    "source": "backend_cards_vote",
	    "subType": "${push_sub_type}",
	    "description": "${global.now_time } push vote card",
	    "timestamp": ${timeStamp?c},
	    <#--  data的压缩方式 0：无压缩 1：MessagePack -->   
	    "compression": ${global.compression},
	    "data": {
	        "content": {
	            <#--  卡片ID，由客户端指定 -->   
	            "id": "vote",
	            <#--  最小版本，目前默认为1 -->   
	            "min_version": "${global.min_version}",
	            "content" : {
	                "url": "${global.server_domain}/cards/api/votes?action=votes&msg_type=1&cmd=${cmd}&card_id=${card.id?c}&push_time=${timeStamp?c}" 
	            }
	        },
	        <#--  消息目标人群肖像属性-->   
	        "target_profile": {
	    		<#include "product.ftl"> 
	    		<#if terminal_limit??>
	    			"terminal_limit":"${terminal_limit?c}",
	    		</#if>
	        	<#if card.trigger_force??>
		        	<#if card.trigger_force == 0>
		        		"enforcement":"false",
		        	</#if>
		        	<#if card.trigger_force == 1>
		        		"enforcement":"true",
		        	</#if>
	        	</#if>
	            <#--  非热门投票推送消息时 需要tvid 推送结果不需要 -->
	            <#if card.hot_vote == 0 && push_sub_type == 1 && card.tv_id??>
	            "tv_id": "${card.tv_id?c}",
	            "column_id": "${card.column_id?c}",
	            <#else>
	                <#if tv_list?? && push_sub_type == 1>
	                "tv_ids":[
	                    <#list tv_list as tv>
	                        "${tv.tv_id?c}"<#if tv_has_next>,</#if>
	                    </#list>
	                ],
	                </#if>
	            </#if>
	            <#if labels_list??>
	            "labels":[
	              <#list labels_list as label>
	              "${label.labelId?c}"<#if label_has_next>,</#if>
	              </#list>
	            ],
	            </#if>
	            <#--  指定投票ID（卡片ID）。-->   
	            "card_id": "${card.id?c}" 
	        }
	    }
	}
```


* 通用卡片消息格式 [返回顶部](#top)

<span id = "general">

```

    <#import "common.ftl" as global/>
    <#if card.sub_type?? && card.sub_type == 2>
    	<#assign cmd ="get_general_qrcode_card"/>
    <#else>
    	<#assign cmd ="get_general_card"/>
    </#if>
    
    {
        "id": "${card.id?c}",
        "type": 8,
        "source": "backend_cards_general",
        "subType": 1,
        "description": "${global.now_time } push general card",
        "timestamp": ${timeStamp?c},
        <#--  data的压缩方式 0：无压缩 1：MessagePack -->   
        "compression": ${global.compression},
        "data": {
            "content": {
                <#--  卡片ID，由客户端指定 -->   
                "id": "general",
                <#--  最小版本，目前默认为1 -->   
                "min_version": "${global.min_version}",
                "content" : {
                    "url": "${global.server_domain}/cards/api/instants?action=instants&cmd=${cmd}&card_id=${card.id?c}&push_time=${timeStamp?c}" 
                }
            },
            <#--  消息目标人群肖像属性-->   
            "target_profile": {
        		<#include "product.ftl"> 
        		<#if terminal_limit??>
        			"terminal_limit":"${terminal_limit?c}",
        		</#if>
                <#if card.tv_id??>
    	            "tv_id": "${card.tv_id?c}",
                  "column_id": "${card.column_id?c}",
                <#else>
                	<#if tv_list??>
                	"tv_ids":[
                		 <#list tv_list as tv>
                         "${tv.tv_id?c}"<#if tv_has_next>,</#if>
                         </#list>
                	],
                </#if>
                </#if>
                <#if area_list??>
                	"areas":[
                		 <#list area_list as area>
                         "${area.area_id?c}"<#if area_has_next>,</#if>
                         </#list>
                	],
                </#if>
                <#if labels_list??>
                    "labels":[
                    <#list labels_list as label>
                        "${label.labelId?c}"<#if label_has_next>,</#if>
                    </#list>
                ],
                </#if>
                <#if user_ids??>
                  	"user_ids":[
                     <#list user_ids as user_id>
                         "${user_id?c}"<#if user_id_has_next>,</#if>
    		        </#list>
               		],
               </#if>
               <#if user_groups_map??>
                	<#list user_groups_map  as key>
    				    "user_groups":[
    				       	"${key.group_id?c}:${key.group_type?c}"
    						<#if key_has_next>,</#if>
    				    ]
    				</#list>
                </#if>
               <#if card.online_delivery??>
    	        	<#if card.online_delivery == 1>
    	        		"online_delivery":"true",
            		<#else>
    	        		"online_delivery":"false",
    	        	</#if>
               </#if>
               <#if card.expired_time??>
            		"expired_time":"${card.expired_time?string("yyyy-MM-dd HH:mm:ss") }",
               </#if>
            	<#if card.trigger_force??>
    	        	<#if card.trigger_force == 0>
    	        		"enforcement":"false"
    	        	</#if>
    	        	<#if card.trigger_force == 1>
    	        		"enforcement":"true"
    	        	</#if>
            	</#if>
            }
        }
    }
```

</span>

* 预约卡片消息格式 [返回顶部](#top)

<span id="subscribe">
```
<#import "common.ftl" as global/>
{
    "id": "${card.recommend_id?c}",
    "type": 10,
    "source": "backend_cards_subscribe",
    "subType": 1,
    "description": "${global.now_time } push subscribe card",
    "timestamp": ${timeStamp?c},
    "compression": ${global.compression},
    "data": {
        "content": {
            <#--  卡片ID，由客户端指定 -->   
            "id": "subscribe",
            <#--  最小版本，目前默认为1 -->   
            "min_version": "${global.min_version}",
            "content" : {
                "url": "${global.server_domain}/cards/api/residents/?action=residents&cmd=get_subscribe_card&msg_type=1&recommend_id=${card.recommend_id?c}&push_time=${timeStamp?c}" 
            }
        },
        <#--  消息目标人群肖像属性-->   
        "target_profile": {
    		<#include "product.ftl"> 
    		<#if terminal_limit??>
    			"terminal_limit":"${terminal_limit?c}",
    		</#if>
            <#if card.tv_id??>
	            "tv_id": "${card.tv_id?c}",
            </#if>
            "card_id": "${card.recommend_id?c}",
        	<#if card.trigger_force??>
	        	<#if card.trigger_force == 0>
	        		"enforcement":"false"
	        	</#if>
	        	<#if card.trigger_force == 1>
	        		"enforcement":"true"
	        	</#if>
        	</#if>
        }
    }
}
```
</span>

* 竞猜答题消息格式 [返回顶部](#top)
<span id="quiz">

```
<#import "common.ftl" as global/>
<#if push_sub_type == 1>
	<#assign cmd ="get_question"/>
</#if>
<#if push_sub_type == 2>
	<#assign cmd ="get_answer"/>
</#if>

{
    "id": "${card.id?c}",
    <#--  卡片消息 竞猜答题 ：18 -->   
    "type": 18,
    <#--  卡片子类型 1 消息 2 结果-->   
    "source": "backend_cards_quiz",
    "subType": "${push_sub_type}",
    "description": "${global.now_time } 推送竞猜答题",
    "timestamp": ${timeStamp?c},
    <#--  data的压缩方式 0：无压缩 1：MessagePack -->   
    "compression": ${global.compression},
    "data": {
        "content": {
            <#--  卡片ID，由客户端指定 -->   
            "id": "quiz",
            <#--  最小版本，目前默认为1 -->   
            "min_version": "${global.min_version}",
            "content" : {
                    <#if push_sub_type == 1>
                        "url": "${global.server_domain}/cards/api/quiz?action=quiz&cmd=${cmd}&card_id=${card.id?c}&push_time=${timeStamp?c}"
                    </#if>
                    <#if push_sub_type == 2>
                        "url": "${global.server_domain}/cards/api/quiz?action=quiz&type=c&cmd=${cmd}&card_id=${card.id?c}&push_time=${timeStamp?c}"
                    </#if>
            }
        },
        <#--  消息目标人群肖像属性-->   
        "target_profile": {
    		<#include "product.ftl"> 
    		<#if terminal_limit??>
    			"terminal_limit":"${terminal_limit?c}",
    		</#if>
        	<#if card.trigger_force??>
	        	<#if card.trigger_force == 0>
	        		"enforcement":"false",
	        	</#if>
	        	<#if card.trigger_force == 1>
	        		"enforcement":"true",
	        	</#if>
        	</#if>

            <#--  指定用户当前的电视台ID。-->
            <#--推送消息是 需要tvid和columnid 推送结果时 不需要-->
            <#if card.tv_id?? && push_sub_type == 1>
                "tv_id": "${card.tv_id?c}",
                "column_id":"${card.column_id?c}",
            <#else>
                <#if tv_list??>
                "tv_ids":[
                    <#list tv_list as tv>
                    "${tv.tv_id?c}"<#if tv_has_next>,</#if>
                    </#list>
                ],
                </#if>
            </#if>
            <#if area_list??>
            	"areas":[
            		 <#list area_list as area>
                     "${area.area_id?c}"<#if area_has_next>,</#if>
                     </#list>
            	],
            </#if>
            <#if labels_list??>
            "labels":[
                <#list labels_list as label>
                "${label.labelId?c}"<#if label_has_next>,</#if>
                </#list>
            ],
            </#if>
            <#if user_ids??>
                "user_ids":[
                    <#list user_ids as user_id>
                    "${user_id?c}"<#if user_id_has_next>,</#if>
                    </#list>
                ],
            </#if>
            <#if user_groups_map??>
                <#list user_groups_map  as key>
                "user_groups":[
                "${key.group_id?c}:${key.group_type?c}"
                    <#if key_has_next>,</#if>
                ]
                </#list>
            </#if>

            <#--  指定投票ID（卡片ID）。-->   
            "card_id": "${card.id?c}" 
        }
    }
}
```
* 礼品商城卡片消息格式 [返回顶部](#top)
<span id="points_mall">

```
<#import "common.ftl" as global/>
{
    "id": "${card.id?c}",
    "type": 13,
    "source": "backend_cards_integral_mall",
    "subType": 1,
    "description": "${global.now_time } push mall card",
    "timestamp": ${timeStamp?c},
    <#--  data的压缩方式 0：无压缩 1：MessagePack -->   
    "compression": ${global.compression},
    "data": {
        "content": {
            "id": "points_mall",
            "min_version": "${global.min_version}",
            "content" : {
                "url": "${global.server_domain}/cards/api/malls/?action=malls&cmd=get_mall_card&msg_type=1&card_id=${card.id?c}&push_time=${timeStamp?c}" 
            }
        },
        <#--  消息目标人群肖像属性-->   
        "target_profile": {
    		<#include "product.ftl"> 
    		<#if terminal_limit??>
    			"terminal_limit":"${terminal_limit?c}",
    		</#if>
        	<#if card.trigger_force??>
	        	<#if card.trigger_force == 0>
	        		"enforcement":"false"
	        	</#if>
	        	<#if card.trigger_force == 1>
	        		"enforcement":"true"
	        	</#if>
        	</#if>
        	<#--  指定用户当前的电视台ID。-->
            <#--推送消息是 需要tvid和columnid 推送结果时 不需要-->
            <#if card.tv_id?? && push_sub_type == 1>
                "tv_id": "${card.tv_id?c}",
                "column_id":"${card.column_id?c}",
            <#else>
                <#if tv_list??>
                "tv_ids":[
                    <#list tv_list as tv>
                    "${tv.tv_id?c}"<#if tv_has_next>,</#if>
                    </#list>
                ],
                </#if>
            </#if>
            <#if area_list??>
            	"areas":[
            		 <#list area_list as area>
                     "${area.area_id?c}"<#if area_has_next>,</#if>
                     </#list>
            	],
            </#if>
            <#if user_ids??>
              	"user_ids":[
                 <#list user_ids as user_id>
                     "${user_id?c}"<#if user_id_has_next>,</#if>
		        </#list>
           		],
           </#if>
           <#if user_groups_map??>
            	<#list user_groups_map  as key>
				    "user_groups":[
				       	"${key.group_id?c}:${key.group_type?c}"
						<#if key_has_next>,</#if>
				    ]
				</#list>
            </#if>
            "card_id": "${card.id?c}" 
        }
    }
}
```
* 暴风卡片消息格式 [返回顶部](#top)
<span id ="storm">

```
<#import "common.ftl" as global/>
{
    "id": "${card.id?c}",
    "type": 24,
    "source": "backend_cards_storm",
    "subType": 1,
    "description": "${global.now_time } push storm card",
    "timestamp": ${timeStamp?c},
    <#--  data的压缩方式 0：无压缩 1：MessagePack -->   
    "compression": ${global.compression},
    "data": {
        "content": {
            <#--  卡片ID，由客户端指定 -->   
            "id": "recommend",
            <#--  最小版本，目前默认为1 -->   
            "min_version": "${global.min_version}",
            "content" : {
                "url": "${global.server_domain}/cards/api/recommends/?action=recommends&cmd=get_message&card_id=${card.id?c}&push_time=${timeStamp?c}" 
            }
        },
        <#--  消息目标人群肖像属性-->   
        "target_profile": {
          <#include "product.ftl">
    		<#if terminal_limit??>
    			"terminal_limit":"${terminal_limit?c}",
    		</#if>
        	"tv_id":"${tv_id?c}",
        	"column_id":"${column_id?c}",
            <#if area_list??>
            	"areas":[
            		 <#list area_list as area>
                     "${area.area_id?c}"<#if area_has_next>,</#if>
                     </#list>
            	],
            </#if>
            <#if user_ids??>
              	"user_ids":[
                 <#list user_ids as user_id>
                     "${user_id?c}"<#if user_id_has_next>,</#if>
		        </#list>
           		],
           </#if>
        	<#if card.trigger_force??>
	        	<#if card.trigger_force == 0>
	        		"enforcement":"false"
	        	</#if>
	        	<#if card.trigger_force == 1>
	        		"enforcement":"true"
	        	</#if>
        	</#if>
        }
    }
}
```
* 广告卡片消息格式 [返回顶部](#top)
<span id="advertise">

```
<#import "common.ftl" as global/>
<#assign cmd ="get_ad_card"/>

{
    "id": "${card.id?c}",
    "type": 27,
    "source": "backend_cards_ad",
    "subType": 1,
    "description": "${global.now_time } push ad card",
    "timestamp": ${timeStamp?c},
    <#--  data的压缩方式 0：无压缩 1：MessagePack -->   
    "compression": ${global.compression},
    "data": {
        "content": {
            <#--  卡片ID，由客户端指定 -->   
            "id": "ad",
            <#--  最小版本，目前默认为1 -->   
            "min_version": "${global.min_version}",
            "content" : {
                "url": "${global.server_domain_adcard}?action=ad&cmd=${cmd}&card_id=${card.id?c}&push_time=${timeStamp?c}" 
            }
        },
        <#--  消息目标人群肖像属性-->   
        "target_profile": {
    		<#include "product.ftl"> 
    		<#if terminal_limit??>
    			"terminal_limit":"${terminal_limit?c}",
    		</#if>
            <#if card.tv_id??>
	            "tv_id": "${card.tv_id?c}",
              "column_id": "${card.column_id?c}",
            <#else>
            	<#if tv_list??>
            	"tv_ids":[
            		 <#list tv_list as tv>
                     "${tv.tv_id?c}"<#if tv_has_next>,</#if>
                     </#list>
            	],
            </#if>
            </#if>
            <#if area_list??>
            	"areas":[
            		 <#list area_list as area>
                     "${area.area_id?c}"<#if area_has_next>,</#if>
                     </#list>
            	],
            </#if>
            
            <#if user_ids??>
              	"user_ids":[
                 <#list user_ids as user_id>
                     "${user_id?c}"<#if user_id_has_next>,</#if>
		        </#list>
           		],
           </#if>
           <#if user_groups_map??>
            	<#list user_groups_map  as key>
				    "user_groups":[
				       	"${key.group_id?c}:${key.group_type?c}"
						<#if key_has_next>,</#if>
				    ]
				</#list>
            </#if>
           <#if card.online_delivery??>
	        	<#if card.online_delivery == 1>
	        		"online_delivery":"true",
        		<#else>
	        		"online_delivery":"false",
	        	</#if>
           </#if>
           <#if card.expired_time??>
        		"expired_time":"${card.expired_time?string("yyyy-MM-dd HH:mm:ss") }",
           </#if>
        	<#if card.trigger_force??>
	        	<#if card.trigger_force == 0>
	        		"enforcement":"false"
	        	</#if>
	        	<#if card.trigger_force == 1>
	        		"enforcement":"true"
	        	</#if>
        	</#if>
        }
    }
}
``` 
* product.ftl [返回顶部](#top)
<span id="product">

```
"products": [
    <#if machine_map??>
        <#list machine_map.keySet() as key>
            {
                <#--  产品ID -->   
                "id": "${key?c}",
                <#--  该产品下的目标机型，可以有多个。如果不指定，-->   
                <#--  那么默认会向该产品下的所有机型推送该卡片。-->   
                "models": [
                    <#if machine_map??>
                        <#list machine_map.get(key) as item>
                            <#if item.machine_type!= ''>
                                "${item.machine_type}"
                                <#if item_has_next>,</#if>
                            </#if>
                        </#list>
                    </#if>
                ]
            }
            <#if key_has_next>,</#if>
        </#list>
    </#if>
],
<#if card.test_product?? && card.test_product == 1>
"device_ids": [
   <#if test_product_list??>
        <#list test_product_list as mac>
            "${mac}"<#if mac_has_next>,</#if>
        </#list>
    </#if>
],
</#if>
```
* 节目导视 [返回顶部](#top)
<span id="column_guide">

```
<#import "common.ftl" as global/>
{
    "id": 0,
    "type": 25,
    "source": "column_guide",
    "subType": 1,
    "description": "",
    "timestamp": ${timeStamp?c},
    <#--  data的压缩方式 0：无压缩 1：MessagePack -->
    "compression": ${global.compression},
    "data": {
        "content": {
        <#--  卡片ID，由客户端指定 -->
            "id": "column_guide",
        <#--  最小版本，目前默认为1 -->
            "min_version": "${global.min_version}",
            "content" : {
            "url": "http://tvtest.kuyun.com/cards/api/interface/programguide/intelligent_recommendation"
            }
        },
        <#--  消息目标人群肖像属性-->
        "target_profile": {
            <#if tv_list??>
            "tv_ids":[
                <#list tv_list as tv>
                "${tv}"<#if tv_has_next>,</#if>
                </#list>
            ],
            </#if>
            "stay_channel_time" : "60",
            "enforcement":"false"
        }
    }
}
```
* 主动推荐卡片消息格式 [返回顶部](#top)
<span id="recommend">

```
<#import "common.ftl" as global/>
{
    "id": "${detail.id?c}",
    "type": 8,
    "source": "schedule@general_recommend",
    "subType": 3,
    "description": "<#if detail.template_id==1>upcoming</#if><#if detail.template_id==2>hot</#if><#if detail.template_id==3>prolist</#if><#if detail.template_id==4>realtime</#if><#if detail.template_id==5>inflow</#if><#if detail.template_id==6>daily</#if><#if detail.template_id==7>rest</#if><#if detail.template_id==8>lunch</#if><#if detail.template_id==9>supper</#if><#if detail.template_id==10>recent_hot</#if>",
    "timestamp": ${timeStamp?c},
    <#--  data的压缩方式 0：无压缩 1：MessagePack -->
    "compression": ${global.compression},
    "data": {
        "content": {
        <#--  卡片ID，由客户端指定 -->
            "id": "general",
        <#--  最小版本，目前默认为1 -->
            "min_version": "${global.min_version}",
            "content" : {
            "url": "${global.server_domain}/cards/api/instants?action=instants&cmd=get_general_card_template&card_id=${detail.id?c}&template_id=${detail.template_id?c}&push_source=general_card_template&push_time=${timeStamp?c}"
            }
        },
        <#--  消息目标人群肖像属性-->
        "target_profile": {
        <#if detail.test_product?? && detail.test_product == 1>
            "device_ids": [
            <#if test_product_list??>
            <#list test_product_list as mac>
                "${mac}"<#if mac_has_next>,</#if>
            </#list>
            </#if>
            ],
        </#if>
            "products": [
                <#if model_map??>
                    <#list model_map.keySet() as key>
                    {
                    <#--  产品ID -->
                    "id": "${key?c}",
                    <#--  该产品下的目标机型，可以有多个。如果不指定，-->
                    <#--  那么默认会向该产品下的所有机型推送该卡片。-->
                    "models": [
                        <#if model_map??>
                            <#list model_map.get(key) as item>
                                <#if item!= '' && item!='0'>
                                "${item}"<#if item_has_next>,</#if>
                                </#if>
                            </#list>
                        </#if>
                    ]
                    }
                        <#if key_has_next>,</#if>
                    </#list>
                </#if>
            ],
            <#if tv_list??>
            "tv_ids":[
                <#list tv_list as tv>
                "${tv}"<#if tv_has_next>,</#if>
                </#list>
            ],
            </#if>
            <#if area_list??>
            "areas":[
                <#list area_list as area>
                "${area.area_id?c}"<#if area_has_next>,</#if>
                </#list>
            ],
            </#if>
            <#if limit_map??>
            "push_count_limit": {
                <#list limit_map.keySet() as key>
                "${key?c}": "${limit_map.get(key)?c}"<#if key_has_next>,</#if>
                </#list>
            },
            </#if>
            "template_id":"${detail.template_id?c}",
            "push_hot_column_limit" : <#if detail.template_id==2||detail.template_id==3||detail.template_id==6||detail.template_id==8||detail.template_id==9||detail.template_id==10>"${detail.quantity?c}"<#else>"0"</#if>,
            "push_global_interval_seconds" : {
              <#list interval_limit_map.keySet() as key>
                "${key?c}": "${interval_limit_map.get(key)?c}"<#if key_has_next>,</#if>
                </#list>
            },
            <#if detail.template_id!=1&&detail.template_id!=5>
                <#if detail.push_over_time??>
                    <#if detail.push_over_time!=0>"stay_channel_time" : "${detail.push_over_time?c}",</#if>
                </#if>
            </#if>
            <#if detail.template_id==7>
              <#if detail.push_interval_time!=0>"push_card_interval_time" : "${detail.push_interval_time?c}",</#if>
            </#if>
            "enforcement":"false"
        }
    }
}
```
* 模块化卡片消息格式 [返回顶部](#top)

<span id="component">

```
<#import "common.ftl" as global/>
{
    "id": "${card.id?c}",
    "type": ${msg_type},
    "source": "backend_cards_components",
    "subType": 1,
    "description": "${global.now_time } push components card",
    "timestamp": ${timeStamp?c},
    <#--  data的压缩方式 0：无压缩 1：MessagePack -->   
    "compression": ${global.compression},
    "data": {
        "content": {
            <#--  卡片ID，由客户端指定 -->   
            "id": "${apk_id}",
            <#--  最小版本，目前默认为1 -->   
            "min_version": "${global.min_version}",
            "content" : {
                "url": "${global.server_domain}${api_url}&msg_type=1&push_time=${timeStamp?c}&combination_id=${card.id?c}&combination_time=${timeStamp?c}"
            }
        },
        <#--  消息目标人群肖像属性-->   
        "target_profile": {
            <#include "product.ftl"> 

            <#if card.tv_id??>
                "tv_id": "${card.tv_id?c}",
              "column_id": "${card.column_id?c}",
            <#else>
                    <#if tv_list??>
                    "tv_ids":[
                       <#list tv_list as tv>
                           "${tv.tv_id?c}"<#if tv_has_next>,</#if>
                           </#list>
                    ],
                    </#if>
            </#if>

            <#if area_list??>
                "areas":[
                     <#list area_list as area>
                     "${area.area_id?c}"<#if area_has_next>,</#if>
                     </#list>
                ],
            </#if>

            <#if user_ids??>
                "user_ids":[
                 <#list user_ids as user_id>
                     "${user_id?c}"<#if user_id_has_next>,</#if>
                  </#list>
                ],
           </#if>

           <#if user_groups_map??>
                <#list user_groups_map  as key>
                  "user_groups":[
                      "${key.group_id?c}:${key.group_type?c}"
                  <#if key_has_next>,</#if>
                  ]
              </#list>
            </#if>

          <#if cardId??>
          "card_id":"${cardId?c}",
          </#if>

            <#if card.triggerForce??>
                <#if card.triggerForce == 0>
                    "enforcement":"false"
                </#if>
                <#if card.triggerForce == 1>
                    "enforcement":"true"
                </#if>
            </#if>
        }
    }
}
```
* 卡片类型值对应表 [返回顶部](#top)
<span id="typeids">


|类型名称|子类型ID|消息类型ID|
|:---:|---|---|
|滚球下注   |1|2|
|滚字卡片   |2|9|
|通用卡片   |3|8|
|卡片列表   |4|无|
|节目信息   |5|12|
|预约卡片   |6|10|
|即将播放   |7|无|
|设置卡片   |8|无|
|个人中心   |9|无|
|礼品商城   |10|13|
|弹幕|11|无|
|体彩|12|无|
|投票卡片   |13|16|
|抽奖|14|无|
|竞猜答题   |15|18|
|抢购|16|无|
|红包|22|无|
|活动|23|无|
|视频推荐   |24|24|
|卡片互联   |25|无|
|在线播放   |101|无|
|节目导视   |500|25|
|游戏人生   |501|26|
|主动推荐   |502|无|
|广告卡片   |503|27|
|模块化卡片|504|28|
