---
layout: post
title: "Dozer的使用方法"
date: 2013-09-07 11:53
comments: truec
categories: JAVA框架
---
Dozer是apache下的一个bean转换类库，按照公司内的大牛的说法它功能远胜于BeanUtils在bean转换方式，而且它极易与spring集成。所以做项目的时候直接选择了它作为bean转化的工具类。下面先介绍一下他的简单用法。<br>
首先，你需要引入jar包，在pom中添加如下依赖：
{% codeblock lang:xml %}
<code>
 <dependency>
          <groupId>net.sf.dozer</groupId>
            <artifactId>dozer</artifactId>
            <version>5.3.2</version>
        </dependency>
        <dependency>
            <groupId>dom4j</groupId>
            <artifactId>dom4j</artifactId>
            <version>1.6</version>
        </dependency>
        <dependency>
            <groupId>jaxen</groupId>
            <artifactId>jaxen</artifactId>
            <version>1.1.1</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.5.8</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>jcl-over-slf4j</artifactId>
            <version>1.5.8</version>
     </dependency>
   </code>
{% endcodeblock %}
'''需要在spring配置文件中这样配置：'''
{% codeblock lang:xml %}
   <bean id="dozerMapper" class="org.dozer.spring.DozerBeanMapperFactoryBean">
        <property name="mappingFiles">
            <list>
                <value>classpath*:dozer-BeanMapping.xml</value>
            </list>
        </property>
    </bean>
 {% endcodeblock %}
'''在类路径下需要这样一个配置文件dozer-BeanMapping.xml，内容如下：'''
{% codeblock lang:xml %}
<?xml version="1.0" encoding="UTF-8"?>
<mappings xmlns="http://dozer.sourceforge.net" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://dozer.sourceforge.net
          http://dozer.sourceforge.net/schema/beanmapping.xsd">
	<configuration>
		<stop-on-errors>true</stop-on-errors>
		<date-format>yyyy-MM-dd HH:mm</date-format>
		<wildcard>true</wildcard>
	</configuration>
</mappings>
{% endcodeblock %}
然后再spring的bean中这样注入：
{%codeblock lang:java %}
   @Autowired
    private Mapper dozzerMap;
{% endcodeblock %}
使用方法大致是这样的：
{%codeblock lang:java %}
  BookCondition bookCondition = dozzerMap.map(bookQueryDto, BookCondition.class);
{% endcodeblock %}
好学的同学会发现现在的用法好像都是默认的没有什么配置，当然如果仅是这么简单的用法，我也不会在这边写这么长时间的wiki了。当然有很多和配置的东西了。
例如A类中的x属性需要和B类中的y属性转换就可以这样配置：
{% codeblock lang:xml %}
 <mapping>
        <class-a>
           A
        </class-a>
        <class-b>
         B
        </class-b>
        <field>
            <a>
                x
            </a>
            <b>
                y
            </b>
        </field>
    </mapping>
{% endcodeblock %}
这样就能实现x和y之间同类型的转换。但是这样的同样类型之间的转换又太过简单怎么可能满足我们的需求。当然作为一个框架肯定有可定制的东西了，于是就有了custom-converter——自定义转换器。例如在VO中是String类型productCodes（以逗号或者换行分割的字符串）需要转化到TO中的productCodes确是一个List。这时就需要我们的custom-converter标签了。配置方法是这样的：
{% codeblock lang:xml %}
 <mapping>
        <class-a>
            com.jd.vc.sub.report.vo.stockSales.OtherStockSalesQueryVo
        </class-a>
        <class-b>
            com.jd.vc.reports.OtherQueryDto
        </class-b>
        <field custom-converter="com.jd.vc.sub.report.util.StringAndListDozerConverter">
            <a>
                productCodes
            </a>
            <b>
                productCodes
            </b>
        </field>
    </mapping>
{% endcodeblock %}
当然这样的标签式需要手写代码支持，代码是这样的：
{%codeblock lang:java %}
package com.jd.vc.sub.report.util;

import org.apache.commons.collections.CollectionUtils;
import org.apache.commons.lang.StringUtils;
import org.dozer.DozerConverter;

import java.util.Arrays;
import java.util.List;

/**
 * @author youpengfeiinfo
 * @version 13-3-20
 */

public class StringAndListDozerConverter extends DozerConverter<String, List> {
    public StringAndListDozerConverter() {
        super(String.class, List.class);
    }

    @Override
    public List<String> convertTo(String source, List destination) {
        destination= Arrays.asList(StringUtils.split(source, "\r\n, "));
        return destination;
    }

    @Override
    public String convertFrom(List source, String destination) {
        return null;
    }
}
{% endcodeblock %}
该类需要实现一个DozerConverter的父类，还必须有个空的构造方法，该构造方法必须实例化一个带有两个参数的构造函数。这两个参数都是类型，第一个是转换属性的类型，第二个是被转换的属性的类型。该类还需要重载两个方法，一个叫convertTo，另一个叫convertFrom，因为项目中只用到了从VO到TO的转换，所以我只重写了convertTo方法。
以上就是Dozer的基本使用方法，还是那句话希望这篇文字能够起到抛砖引玉的作用.
