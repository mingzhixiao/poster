#### mybatis-generator工具

##### 1.引入mybatis的pom

```xml
<dependency>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-core</artifactId>
    <version>1.3.7</version>
</dependency>
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
```

##### 2.引入mybatis-generator.xml自动生成文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://www.mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!-- 数据库驱动:选择你的本地硬盘上面的数据库驱动包-->
    <classPathEntry  location="C:\Users\Administrator\Desktop\mysql-connector-java-8.0.13.jar"/>
    <context id="DB2Tables"  targetRuntime="MyBatis3">
        <!--使用自带的序列化插件-->
        <plugin type="org.mybatis.generator.plugins.SerializablePlugin"/>
        <plugin type="com.wzw.utils.LombokPlugin"/>
        <commentGenerator type="com.wzw.utils.MyCommentGenerator">
        </commentGenerator>
        <!--数据库链接URL，用户名、密码 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver" connectionURL="jdbc:mysql://localhost/manager?tinyInt1isBit=false&amp;serverTimezone=GMT%2B8" userId="root" password="root">
        </jdbcConnection>
        <javaTypeResolver type="com.wzw.utils.TingIntResolverInteger">
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>
        <!-- 生成模型的包名和位置-->
        <javaModelGenerator targetPackage="com.wzw.entity" targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <!-- 生成映射文件的包名和位置-->
        <sqlMapGenerator targetPackage="com.wzw.mapper" targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>
        <!-- 生成DAO的包名和位置-->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.wzw.mapper" targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>
        <!-- 要生成的表 tableName是数据库中的表名或视图名 domainObjectName是实体类名-->
        <table tableName="t_test" domainObjectName="Test" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false"></table>

    </context>
</generatorConfiguration>
```

##### 3.引入生自定插件lombok

```java
package com.wzw.utils;

import org.mybatis.generator.api.IntrospectedColumn;
import org.mybatis.generator.api.IntrospectedTable;
import org.mybatis.generator.api.PluginAdapter;
import org.mybatis.generator.api.dom.java.FullyQualifiedJavaType;
import org.mybatis.generator.api.dom.java.Interface;
import org.mybatis.generator.api.dom.java.Method;
import org.mybatis.generator.api.dom.java.TopLevelClass;

import java.util.*;

public class LombokPlugin extends PluginAdapter {

    private final Collection<Annotations> annotations;

    public LombokPlugin() {
        annotations = new LinkedHashSet<Annotations>(Annotations.values().length);
    }


    public boolean validate(List<String> warnings) {
        return true;
    }


    @Override
    public boolean modelBaseRecordClassGenerated(
            TopLevelClass topLevelClass,
            IntrospectedTable introspectedTable
    ) {
        addAnnotations(topLevelClass);
        return true;
    }


    @Override
    public boolean modelPrimaryKeyClassGenerated(
            TopLevelClass topLevelClass,
            IntrospectedTable introspectedTable
    ) {
        addAnnotations(topLevelClass);
        return true;
    }


    @Override
    public boolean modelRecordWithBLOBsClassGenerated(
            TopLevelClass topLevelClass,
            IntrospectedTable introspectedTable
    ) {
        addAnnotations(topLevelClass);
        return true;
    }


    @Override
    public boolean modelGetterMethodGenerated(
            Method method,
            TopLevelClass topLevelClass,
            IntrospectedColumn introspectedColumn,
            IntrospectedTable introspectedTable,
            ModelClassType modelClassType
    ) {
        return false;
    }

    @Override
    public boolean modelSetterMethodGenerated(
            Method method,
            TopLevelClass topLevelClass,
            IntrospectedColumn introspectedColumn,
            IntrospectedTable introspectedTable,
            ModelClassType modelClassType
    ) {
        return false;
    }


    private void addAnnotations(TopLevelClass topLevelClass) {
        for (Annotations annotation : annotations) {
            topLevelClass.addImportedType(annotation.javaType);
            topLevelClass.addAnnotation(annotation.asAnnotation());
        }
    }

    @Override
    public void setProperties(Properties properties) {
        super.setProperties(properties);

        //@Data is default annotation
        annotations.add(Annotations.DATA);

        for (String annotationName : properties.stringPropertyNames()) {
            if (annotationName.contains(".")) {
                // Not an annotation name
                continue;
            }
            String value = properties.getProperty(annotationName);
            if (!Boolean.parseBoolean(value)) {
                // The annotation is disabled, skip it
                continue;
            }
            Annotations annotation = Annotations.getValueOf(annotationName);
            if (annotation == null) {
                continue;
            }
            String optionsPrefix = annotationName + ".";
            for (String propertyName : properties.stringPropertyNames()) {
                if (!propertyName.startsWith(optionsPrefix)) {
                    // A property not related to this annotation
                    continue;
                }
                String propertyValue = properties.getProperty(propertyName);
                annotation.appendOptions(propertyName, propertyValue);
                annotations.add(annotation);
                annotations.addAll(Annotations.getDependencies(annotation));
            }
        }
    }

    @Override
    public boolean clientGenerated(
            Interface interfaze,
            TopLevelClass topLevelClass,
            IntrospectedTable introspectedTable
    ) {
        interfaze.addImportedType(new FullyQualifiedJavaType(
                "org.apache.ibatis.annotations.Mapper"));
        interfaze.addAnnotation("@Mapper");
        return true;
    }

    private enum Annotations {
        DATA("data", "@Data", "lombok.Data"),
        BUILDER("builder", "@Builder", "lombok.Builder"),
        ALL_ARGS_CONSTRUCTOR("allArgsConstructor", "@AllArgsConstructor", "lombok.AllArgsConstructor"),
        NO_ARGS_CONSTRUCTOR("noArgsConstructor", "@NoArgsConstructor", "lombok.NoArgsConstructor"),
        ACCESSORS("accessors", "@Accessors", "lombok.experimental.Accessors"),
        TO_STRING("toString", "@ToString", "lombok.ToString");


        private final String paramName;
        private final String name;
        private final FullyQualifiedJavaType javaType;
        private final List<String> options;


        Annotations(String paramName, String name, String className) {
            this.paramName = paramName;
            this.name = name;
            this.javaType = new FullyQualifiedJavaType(className);
            this.options = new ArrayList<String>();
        }

        private static Annotations getValueOf(String paramName) {
            for (Annotations annotation : Annotations.values())
                if (String.CASE_INSENSITIVE_ORDER.compare(paramName, annotation.paramName) == 0)
                    return annotation;

            return null;
        }

        private static Collection<Annotations> getDependencies(Annotations annotation) {
            if (annotation == ALL_ARGS_CONSTRUCTOR)
                return Collections.singleton(NO_ARGS_CONSTRUCTOR);
            else
                return Collections.emptyList();
        }

        // A trivial quoting.
        // Because Lombok annotation options type is almost String or boolean.
        private static String quote(String value) {
            if (Boolean.TRUE.toString().equals(value) || Boolean.FALSE.toString().equals(value))
                // case of boolean, not passed as an array.
                return value;
            return value.replaceAll("[\\w]+", "\"$0\"");
        }

        private void appendOptions(String key, String value) {
            String keyPart = key.substring(key.indexOf(".") + 1);
            String valuePart = value.contains(",") ? String.format("{%s}", value) : value;
            this.options.add(String.format("%s=%s", keyPart, quote(valuePart)));
        }

        private String asAnnotation() {
            if (options.isEmpty()) {
                return name;
            }
            StringBuilder sb = new StringBuilder();
            sb.append(name);
            sb.append("(");
            boolean first = true;
            for (String option : options) {
                if (first) {
                    first = false;
                } else {
                    sb.append(", ");
                }
                sb.append(option);
            }
            sb.append(")");
            return sb.toString();
        }
    }
}
```

##### 4.自定义属性映射

```java
package com.wzw.utils;

import org.mybatis.generator.api.dom.java.FullyQualifiedJavaType;
import org.mybatis.generator.internal.types.JavaTypeResolverDefaultImpl;

import java.sql.Types;

/**
 * @Description TODO
 * @Date 2019/8/24 22:36
 * @Created by wzw
 */
public class TingIntResolverInteger extends JavaTypeResolverDefaultImpl {
    public TingIntResolverInteger() {
        super();
        //把数据库的 TINYINT 映射成 Integer
        super.typeMap.put(Types.TINYINT, new JavaTypeResolverDefaultImpl.JdbcTypeInformation("TINYINT", new FullyQualifiedJavaType(Integer.class.getName())));
    }
}
```

##### 5.自定义注释生成

```java
package com.wzw.utils;

import org.mybatis.generator.api.IntrospectedColumn;
import org.mybatis.generator.api.IntrospectedTable;
import org.mybatis.generator.api.dom.java.Field;
import org.mybatis.generator.api.dom.java.InnerClass;
import org.mybatis.generator.api.dom.java.Method;
import org.mybatis.generator.internal.DefaultCommentGenerator;

import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Properties;

/**
 * @Description TODO
 * @Date 2019/8/24 22:45
 * @Created by wzw
 */
public class MyCommentGenerator extends DefaultCommentGenerator {
    private Properties properties;
    private Properties systemPro;
    private boolean suppressDate;
    private boolean suppressAllComments;
    private String currentDateStr;

    public MyCommentGenerator() {
        super();
        properties = new Properties();
        systemPro = System.getProperties();
        suppressDate = false;
        suppressAllComments = false;
        currentDateStr = (new SimpleDateFormat("yyyy-MM-dd")).format(new Date());
    }


    public void addFieldComment(Field field, IntrospectedTable introspectedTable,
                                IntrospectedColumn introspectedColumn) {
        if (suppressAllComments) {
            return;
        }
        StringBuilder sb = new StringBuilder();
        field.addJavaDocLine("/**");
        sb.append(" * ");
        sb.append(introspectedColumn.getRemarks());
        field.addJavaDocLine(sb.toString().replace("\n", " "));
        field.addJavaDocLine(" */");
    }

    public void addFieldComment(Field field, IntrospectedTable introspectedTable) {

    }

    public void addGeneralMethodComment(Method method, IntrospectedTable introspectedTable) {

    }

    public void addGetterComment(Method method, IntrospectedTable introspectedTable,
                                 IntrospectedColumn introspectedColumn) {

    }

    public void addSetterComment(Method method, IntrospectedTable introspectedTable,
                                 IntrospectedColumn introspectedColumn) {

    }

    public void addClassComment(InnerClass innerClass, IntrospectedTable introspectedTable, boolean markAsDoNotDelete) {

    }

    public void addClassComment(InnerClass innerClass, IntrospectedTable introspectedTable) {
    }


}
```

##### 6.mybatis-generator运行类

```java
package com.wzw.utils;

import org.mybatis.generator.api.MyBatisGenerator;
import org.mybatis.generator.config.Configuration;
import org.mybatis.generator.config.xml.ConfigurationParser;
import org.mybatis.generator.internal.DefaultShellCallback;

import java.io.InputStream;
import java.util.ArrayList;
import java.util.List;

/**
 * @Description TODO
 * @Date 2019/8/24 23:00
 * @Created by wzw
 */
public class MybatisGeneratorRun {
    public static void main(String[] args) throws Exception{
        MybatisGeneratorRun app = new MybatisGeneratorRun();

        System.out.println(app.getClass().getResource("/").getPath());
        app.generator();
        System.out.println(System.getProperty("user.dir"));
    }

    public void generator() throws Exception{

        List<String> warnings = new ArrayList<String>();
        boolean overwrite = true;
        InputStream resourceAsStream = this.getClass().getClassLoader().getResourceAsStream("generatorConfig.xml");
        ConfigurationParser cp = new ConfigurationParser(warnings);
        Configuration config = cp.parseConfiguration(resourceAsStream);
        DefaultShellCallback callback = new DefaultShellCallback(overwrite);
        MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
        myBatisGenerator.generate(null);

        for(String warning:warnings){
            System.out.println(warning);
        }
    }
}
```

