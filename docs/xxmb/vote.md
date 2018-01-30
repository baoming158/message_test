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
