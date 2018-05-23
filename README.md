# 23 May from Qzh
#### nexus代理库 自己用direct 在sts配置里也要更改。
- mvn -Pnexus dependency:tree
- mvn -Pnexus dependency:resolve
- mvn -Pnexus dependency:sources  

**firstDay**  

1. `one warning:plugins少个s在本地仓库unfind`  

2. *read Reference* 

3.  @component写在传统的bean里按照以下方法写
  @Bean
	public AnnotationBean annotationBean() {
		AnnotationBean annotationBean = new AnnotationBean();
		annotationBean.setP1(99);
		annotationBean.setP2("Name");
		annotationBean.setDataSource(dataSource());
		return annotationBean;
	}  
  
  ## 看第14章
