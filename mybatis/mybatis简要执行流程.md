Mybatis执行流程（简略）

1.加载配置文件 =>  Resource.getResourceAsStream("mybatis-config.xml");
2.获得DefaultSqlSessionFactory   SQLSessionFactoryBuilder.build()；
3.获得Session => defaultSqlSessionFactory.openSession();
3.获得Mapper  => session.getMapper(clazz);
4.执行Mappper中的方法 mapper.queryAll();

Mybatis执行查询流程
  SqlSessionFactoryBuilder().build(inputStream);
    => new XMLConfigBuilder(inputStream, environment, properties).parse();(Configuration);
	   => parseConfiguration(parser.evalNode("/configuration"));
	       => mapperElement(root.evalNode("mappers"));
		       => new XMLMapperBuilder(inputStream, configuration, resource, configuration.getSqlFragments()).parse();
			       => bindMapperForNamespace();
				       => configuration.addMapper(boundType);
					       => knownMappers.put(type, new MapperProxyFactory<T>(type));
						      new MapperAnnotationBuilder(config, type).parse();
							    =>parseStatement(method);
							       => configuration.addMappedStatement(statement); 
								        => mappedStatements.put(shortKey, value);
									       mappedStatements.put(key, value);
							  
	  new DefaultSqlSessionFactory(config);  (DefaultSqlSessionFactory)

  defaultSqlSessionFactory.openSession() 
   => openSessionFromDataSource(ExecutorType,TransactionIsolationLevel,autoCommit)
	   => config.newExecutor(Transaction,ExecutorType,autoCommit); (Executor)
	      new DefaultSqlSession(configuration, executor); (DefaultSqlSession)
  session.getMapper(clazz)
	=> knownMappers.get(clazz);(MapperProxyFactory<T>)
	   Proxy.newProxyInstance(mapperInterface.getClassLoader(), new Class[] { mapperInterface }, mapperProxy); (MapperProxy);
	   
  mapper.queryAll()
    => mapperMethod.execute(sqlSession, args);  (由MapperProxy执行)
		=> executeForMany(sqlSession, args);
		    => sqlSession.<E>selectList(command.getName(), param, rowBounds);
				 => ms = configuration.getMappedStatement(statement);
			        executor.<E>query(ms, wrapCollection(parameter), rowBounds, Executor.NO_RESULT_HANDLER);
				    => queryFromDatabase(ms, parameter, rowBounds, resultHandler, key, boundSql);
					    => doQuery(ms, parameter, rowBounds, resultHandler, boundSql);
						     => configuration.newStatementHandler(this, ms, parameter, rowBounds, resultHandler, boundSql);(StatementHandler)  (由SimpleExecutor)
							    handler.<E>query(stmt, resultHandler);
							     => PreparedStatement.execute();
								    resultSetHandler.<E> handleResultSets(ps);
																			
	SqlSession 四大执行对象  Executor  ParameterHandler StatementHandler ResultSetHandler
	

  
	
									
					
  	
  
		   


