---
layout: post
title: <h5>Apache Commons 工具集使用简介(转载收藏)</h5>
---

Apache Commons包含了很多开源的工具，用于解决平时编程经常会遇到的问题，减少重复劳动。我选了一些比较常用的项目做简单介绍。文中用了很多网上现成的东西，我只是做了一个汇总整理。

一、Commons BeanUtils

	http://jakarta.apache.org/commons/beanutils/index.html

说明：针对Bean的一个工具集。由于Bean往往是有一堆get和set组成，所以BeanUtils也是在此基础上进行一些包装。

使用示例：功能有很多，网站上有详细介绍。一个比较常用的功能是Bean Copy，也就是copy bean的属性。如果做分层架构开发的话就会用到，比如从PO（Persistent Object）拷贝数据到VO（Value Object）。

传统方法如下：

	//得到TeacherForm

	TeacherForm teacherForm=(TeacherForm)form;

	//构造Teacher对象

	Teacher teacher=new Teacher();

	//赋值
	teacher.setName(teacherForm.getName());
	teacher.setAge(teacherForm.getAge());
	teacher.setGender(teacherForm.getGender());
	teacher.setMajor(teacherForm.getMajor());
	teacher.setDepartment(teacherForm.getDepartment());

	//持久化Teacher对象到数据库
	HibernateDAO= ;
	HibernateDAO.save(teacher);
	使用BeanUtils后，代码就大大改观了，如下所示：

	//得到TeacherForm
	TeacherForm teacherForm=(TeacherForm)form;
	//构造Teacher对象
	Teacher teacher=new Teacher();

	//赋值
	BeanUtils.copyProperties(teacher,teacherForm);

	//持久化Teacher对象到数据库
	HibernateDAO= ;
	HibernateDAO.save(teacher);


二、Commons CLI

	http://jakarta.apache.org/commons/cli/index.html

说明：这是一个处理命令的工具。比如main方法输入的string[]需要解析。你可以预先定义好参数的规则，然后就可以调用CLI来解析。

使用示例：

	// create Options object
	Options options = new Options();
	// add t option, option is the command parameter, false indicates that
	// this parameter is not required.

	options.addOption(“t”, false, “display current time”);
	options.addOption("c", true, "country code");

	CommandLineParser parser = new PosixParser();
	CommandLine cmd = parser.parse( options, args);

	if(cmd.hasOption("t")) {
	   // print the date and time
	}else {
	   // print the date
	}

	// get c option value
	String countryCode = cmd.getOptionValue("c");

	if(countryCode == null) {
	    // print default date
	}else {
	    // print date for country specified by countryCode
	}



三、Commons Codec

	http://jakarta.apache.org/commons/codec/index.html

说明：这个工具是用来编码和解码的，包括Base64，URL，Soundx等等。用这个工具的人应该很清楚这些，我就不多介绍了。



四、Commons Collections

	http://jakarta.apache.org/commons/collections/

说明：你可以把这个工具看成是java.util的扩展。

使用示例：举一个简单的例子

	OrderedMap map = new LinkedMap();
	map.put("FIVE", "5");
	map.put("SIX", "6");
	map.put("SEVEN", "7");
	map.firstKey(); // returns "FIVE"
	map.nextKey("FIVE"); // returns "SIX"
	map.nextKey("SIX"); // returns "SEVEN"



五、Commons Configuration

	http://jakarta.apache.org/commons/configuration/

说明：这个工具是用来帮助处理配置文件的，支持很多种存储方式

	1. Properties files
	2. XML documents
	3. Property list files (.plist)
	4. JNDI
	5. JDBC Datasource
	6. System properties
	7. Applet parameters
	8. Servlet parameters

使用示例：举一个Properties的简单例子

	# usergui.properties, definining the GUI,
	colors.background = #FFFFFF
	colors.foreground = #000080
	window.width = 500
	window.height = 300

 
	PropertiesConfiguration config = new PropertiesConfiguration("usergui.properties");
	config.setProperty("colors.background", "#000000);
	config.save();

	config.save("usergui.backup.properties);//save a copy
	Integer integer = config.getInteger("window.width");


六、Commons DBCP

	http://jakarta.apache.org/commons/dbcp/

说明：Database Connection pool, Tomcat就是用的这个，不用我多说了吧，要用的自己去网站上看说明。


七、Commons DbUtils

	http://jakarta.apache.org/commons/dbutils/

说明：我以前在写数据库程序的时候，往往把数据库操作单独做一个包。DbUtils就是这样一个工具，以后开发不用再重复这样的工作了。值得一体的是，这个工具并不是现在流行的OR-Mapping工具（比如Hibernate），只是简化数据库操作，比如

	QueryRunner run = new QueryRunner(dataSource);

	// Execute the query and get the results back from the handler
	Object[] result = (Object[]) run.query("SELECT * FROM Person WHERE name=?", "John Doe");


七、Commons FileUpload

	http://jakarta.apache.org/commons/fileupload/

说明：jsp的上传文件功能怎么做呢？

使用示例：

	// Create a factory for disk-based file items
	FileItemFactory factory = new DiskFileItemFactory();
	// Create a new file upload handler
	ServletFileUpload upload = new ServletFileUpload(factory);

	 

	// Parse the request
	List /* FileItem */ items = upload.parseRequest(request);
	// Process the uploaded items
	Iterator iter = items.iterator();
	while (iter.hasNext()) {
	     FileItem item = (FileItem) iter.next();
	     if (item.isFormField()) {
	        processFormField(item);
	     } else {
	        processUploadedFile(item);
	     }
	}


八、Commons HttpClient

	http://jakarta.apache.org/commons/httpclient/

说明：这个工具可以方便通过编程的方式去访问网站。

使用示例：最简单的Get操作

	GetMethod get = new GetMethod("http://jakarta.apache.org");

	// execute method and handle any error responses.

	...

	InputStream in = get.getResponseBodyAsStream();
	// Process the data from the input stream.
	get.releaseConnection();


九、Commons IO

	http://jakarta.apache.org/commons/io/

说明：可以看成是java.io的扩展，我觉得用起来非常方便。

使用示例：

1．读取Stream

标准代码：
	InputStream in = new URL( "http://jakarta.apache.org" ).openStream();
	try {
	       InputStreamReader inR = new InputStreamReader( in );
	       BufferedReader buf = new BufferedReader( inR );
	       String line;
	       while ( ( line = buf.readLine() ) != null ) {
	          System.out.println( line );
	       }
	  } finally {
	    in.close();
	  }
	使用IOUtils

	InputStream in = new URL( "http://jakarta.apache.org" ).openStream();
	try {
	    System.out.println( IOUtils.toString( in ) );
	} finally {
	    IOUtils.closeQuietly(in);
	}


2．读取文件

	File file = new File("/commons/io/project.properties");
	List lines = FileUtils.readLines(file, "UTF-8");


3．察看剩余空间

	long freeSpace = FileSystemUtils.freeSpace("C:/");


十、Commons JXPath

	http://jakarta.apache.org/commons/jxpath/

说明：Xpath你知道吧，那么JXpath就是基于Java对象的Xpath，也就是用Xpath对Java对象进行查询。这个东西还是很有想像力的。

使用示例：

	Address address = (Address)JXPathContext.newContext(vendor).
	getValue("locations[address/zipCode='90210']/address");
	上述代码等同于

	Address address = null;
	Collection locations = vendor.getLocations();
	Iterator it = locations.iterator();
	while (it.hasNext()){
	    Location location = (Location)it.next();
	    String zipCode = location.getAddress().getZipCode();
	    if (zipCode.equals("90210")){
	       address = location.getAddress();
	        break;
	    }
	}
	

十一、Commons Lang

	http://jakarta.apache.org/commons/lang/

说明：这个工具包可以看成是对java.lang的扩展。提供了诸如StringUtils, StringEscapeUtils, RandomStringUtils, Tokenizer, WordUtils等工具类。


十二、Commons Logging

	http://jakarta.apache.org/commons/logging/

说明：你知道log4j吗？



十三、Commons Math

	http://jakarta.apache.org/commons/math/

说明：看名字你就应该知道这个包是用来干嘛的了吧。这个包提供的功能有些和Commons Lang重复了，但是这个包更专注于做数学工具，功能更强大。



十四、Commons Net

	http://jakarta.apache.org/commons/net/

说明：这个包还是很实用的，封装了很多网络协议。

	1. FTP
	2. NNTP
	3. SMTP
	4. POP3
	5. Telnet
	6. TFTP
	7. Finger
	8. Whois
	9. rexec/rcmd/rlogin
	10. Time (rdate) and Daytime
	11. Echo
	12. Discard
	13. NTP/SNTP

使用示例：

	TelnetClient telnet = new TelnetClient();
	telnet.connect( "192.168.1.99", 23 );
	InputStream in = telnet.getInputStream();
	PrintStream out = new PrintStream( telnet.getOutputStream() );
	...
	telnet.close();


十五、Commons Validator

	http://jakarta.apache.org/commons/validator/

说明：用来帮助进行验证的工具。比如验证Email字符串，日期字符串等是否合法。

使用示例：

	// Get the Date validator
	DateValidator validator = DateValidator.getInstance();
	// Validate/Convert the date
	Date fooDate = validator.validate(fooString, "dd/MM/yyyy");
	if (fooDate == null) {
	    // error...not a valid date
	    return;
	}


十六、Commons virtual File System

	http://jakarta.apache.org/commons/vfs/

说明：提供对各种资源的访问接口。支持的资源类型包括

	1. CIFS
	2. FTP
	3. Local Files
	4. HTTP and HTTPS
	5. SFTP
	6. Temporary Files
	7. WebDAV
	8. Zip, Jar and Tar (uncompressed, tgz or tbz2)
	9. gzip and bzip2
	10. res
	11. ram

这个包的功能很强大，极大的简化了程序对资源的访问。

使用示例：

从jar中读取文件

	// Locate the Jar file
	FileSystemManager fsManager = VFS.getManager();
	FileObject jarFile = fsManager.resolveFile( "jar:lib/aJarFile.jar" );

	// List the children of the Jar file
	FileObject[] children = jarFile.getChildren();
	System.out.println( "Children of " + jarFile.getName().getURI() );
	for ( int i = 0; i < children.length; i++ ){
	    System.out.println( children[ i ].getName().getBaseName() );
	}
	
从smb读取文件

	StaticUserAuthenticator auth = new StaticUserAuthenticator("username", "password", null);
	FileSystemOptions opts = new FileSystemOptions();
	DefaultFileSystemConfigBuilder.getInstance().setUserAuthenticator(opts, auth);
	FileObject fo = VFS.getManager().resolveFile("smb://host/anyshare/dir", opts);