Java
=================

如果使用maven，可以加入如下依赖::

	<dependency>
	    <groupId>com.baoquan</groupId>
	    <artifactId>eagle-sdk</artifactId>
	    <version>3.0.19</version>
	</dependency>

如果使用gradle，可以加入如下依赖::
	
	compile group: 'com.baoquan', name: 'eagle-sdk', version: '3.0.16'

初始化客户端
------------------

::

	BaoquanClient client = new BaoquanClient();
	// 设置api地址，比如保全网的测试环境地址
	client.setHost("https://api.baoquan.com");
	// 设置access key
	client.setAccessKey("fsBswNzfECKZH9aWyh47fc"); 
	// 设置rsa私钥文件的绝对路径
	client.setPemPath("path/to/rsa_private.pem");
	// 设置版本
	client.setVersion("v3);

rsa私钥文件应该以 **-----BEGIN PRIVATE KEY-----** 开头和 **-----END PRIVATE KEY-----** 结尾，比如::

	-----BEGIN PRIVATE KEY-----
	MIICeAIBADANBgkqhkiG9w0BAQEFAASCAmIwggJeAgEAAoGBALS8adG98pHSLEq6
	kOT6PG25GMBzpiSs1oXwnPLTOVOYarffF0xSB7nk5yxbqx5BseJNz2NxyTpeJOk8
	FXEI7qTbS6oYAgyH/2HMr5Az3pKGLRdIjJQrpu3qpJkzRw82qGP2MkmVkUYeOl9B
	ZEUpk1GmziwrhbD0zcJITA0mnUqnAgMBAAECgYBnetUPjLTcvrwzURxyrb95hxff
	4JdIuljdOUVzVnKlJUg83JOHVBQuYBvn7thLq4uAqdJK+rQfIhX6IDeaj2WqsO7Y
	d4YoVxFAlfaHIICJKur15KOXuPMpdm3ilZ0c2yCTrJ0m3Xm6mpwd4blDDSupmlj4
	HEXXiInGZgwfTqONAQJBAOlX3EyvE2NvzYMh39wz11fmOi0UiyIvz0immjed4dhV
	0YvPjx8Gj7XGwCkzbuNwr7tlkMTaSiYR8cw1QzV4QoECQQDGSOlgAJC8oUP2+u4H
	+A83jfSLlhQ8XKAJn5Din9kBvs4eKMSjTpJiDBgA7NUAhUfCqS2/m5TiTiS3X3Ij
	ZKknAkEA2iaQCQks4SvnQI9s0FuPGdhdz0ODiCSWb9+CEjkCqdQhococje7+b/0u
	Ldat9uilAlfD7qX96HWiTz4EZXrXAQJBAJ+CbgMl0Ul9bcBUsoHEovEtCEn2TIcW
	eEPlkldNAfSuev+2CiHZhlbLpc+wtdU6YrUNBdl7HjVDabP+W0JvqscCQQDBoUR8
	Y3NUOdGRcaSgwT56tP5J1cZxg1b4vCyr+YfvcEGSBrEaxEugDUjxbON4etMVflh/
	H3QNSvRf4XQ44wQO
	-----END PRIVATE KEY-----

其它初始化设置
^^^^^^^^^^^^^^^

还有些其它可选的初始化设置，比如设置api版本，设置request id生成器，默认情况下你无需进行这些设置::

	// 设置request id生成器，生成器需要实现RequestIdGenerator接口中的createRequestId方法
	client.setRequestIdGenerator(CustomRequestGenerator)
	// sdk中默认的request id生成器
	public class DefaultRequestIdGenerator implements RequestIdGenerator {
		@Override
		public String createRequestId() {
			return UUID.randomUUID().toString();
		}
	}

客户端初始化完成后即可调用客户端中的方法发送请求

创建内容保全
------------------

::

        BaseAttestationPayloadParam payload = new HashAttestationParam();
        payload.setTemplate_id("ceshiuserdata");
        payload.setUnique_id(randomUniqueId());
        Map<IdentityTypeEnum, String> identities = new HashMap<IdentityTypeEnum, String>();
        identities.put(IdentityTypeEnum.ID, "15817112383");
        payload.setIdentities(identities);
        List<PayloadFactoidParam> factoids = new ArrayList<PayloadFactoidParam>();
        PayloadFactoidParam factoid = new PayloadFactoidParam();
        factoid.setUnique_id(randomUniqueId());
        factoid.setType("file");
        LinkedHashMap<String, String> map = new LinkedHashMap<String, String>();
        factoid.setData(map);
        map.put("file_name", "584691132_121_1943880412");
        map.put("size", "330124199501017791");
        factoids.add(factoid);
        payload.setFactoids(factoids);
        try {
            ResultModel response = client.createAttestation(payload);
            System.out.println(Utils.objectToJson(response.getData()));
        } catch (Exception e) {
            e.printStackTrace();
        }


创建文件保全
------------------

::

        BaseAttestationPayloadParam payload = new BaseAttestationPayloadParam();
        payload.setTemplate_id("795eac6a8d88401f8926efdef1a95e33");
        payload.setUnique_id(randomUniqueId());
        Map<IdentityTypeEnum, String> identities = new HashMap<IdentityTypeEnum, String>();
        identities.put(IdentityTypeEnum.ID, "15857112383");
        payload.setIdentities(identities);
        List<PayloadFactoidParam> factoids = new ArrayList<PayloadFactoidParam>();
        PayloadFactoidParam factoid = new PayloadFactoidParam();
        factoid.setUnique_id(randomUniqueId());
        factoid.setType("file");
        LinkedHashMap<String, String> map = new LinkedHashMap<String, String>();
        factoid.setData(map);
        map.put("file_name", "李三");
        map.put("size", "330124199501017791");
        factoids.add(factoid);
        payload.setFactoids(factoids);
        ResultModel response = null;
        InputStream inputStream = new FileInputStream("D:\\452886513210892289.pdf");

        ByteArrayBody byteArrayBody = new ByteArrayBody(IOUtils.toByteArray(inputStream), ContentType.DEFAULT_BINARY, "452886513210892289.pdf");
        try {
            response = client.createAttestationWithFile(payload, byteArrayBody);
            System.out.println(Utils.objectToJson(response.getData()));
        } catch (ServerException e) {
            e.printStackTrace();
        }


创建保全(sha256)
------------------

::

	    HashAttestationParam payload = new HashAttestationParam();
        payload.setTemplate_id("ceshisha256");
        payload.setUnique_id(randomUniqueId());
        Map<IdentityTypeEnum, String> identities = new HashMap<IdentityTypeEnum, String>();
        identities.put(IdentityTypeEnum.ID, "15851112383");
        payload.setIdentities(identities);
        List<PayloadFactoidParam> factoids = new ArrayList<PayloadFactoidParam>();
        PayloadFactoidParam factoid = new PayloadFactoidParam();
        factoid.setUnique_id(randomUniqueId());
        factoid.setType("file");
        LinkedHashMap<String, String> map = new LinkedHashMap<String, String>();
        factoid.setData(map);
        map.put("file_name", "李三");
        map.put("size", "330124199501017791");
        factoids.add(factoid);
        payload.setFactoids(factoids);
        payload.setSha256("654c71176b207401445fdd471f5e021f65af50d7361bf828e5b1219c89b977b0");
        ResultModel response = null;
        try {
            response = client.createAttestationWithSha256(payload);
            System.out.println(Utils.objectToJson(response.getData()));
        } catch (Exception e) {
            e.printStackTrace();
        }

网页取证
------------------

::

      UrlAttestationParam payload = new UrlAttestationParam();
        // 设置保全唯一码
        payload.setUnique_id(randomUniqueId());
        // 设置模板id
        payload.setTemplate_id("4oE5JmY9SJqyieww75rYiW");
        Map<IdentityTypeEnum, String> identities = new HashMap<IdentityTypeEnum, String>();
        identities.put(IdentityTypeEnum.ID, "429006198507104214");
        payload.setIdentities(identities);
        List<PayloadFactoidParam> factoids = new ArrayList<PayloadFactoidParam>();
        PayloadFactoidParam factoid = new PayloadFactoidParam();
        LinkedHashMap<String, String> loanDataMap = new LinkedHashMap<String, String>();
        loanDataMap.put("web_address", "https://jx.tmall.com");
        loanDataMap.put("name", "ceshi");
        factoid.setData(loanDataMap);
        factoid.setUnique_id(randomUniqueId());
        factoid.setType("evidence");
        factoids.add(factoid);
        payload.setFactoids(factoids);
        payload.setUrl("https://www.w3school.com.cn");
        payload.setMode(1);
        payload.setEvidenceName("测试取证");
        payload.setEvidenceLabel("测试取证");
        ResultModel response = client.createAsyAttestationWithUrl(payload);
        System.out.println(response.getData());

网页取证模式二 展示图片
------------------

::

        UrlAttestationStep2Param payload = new UrlAttestationStep2Param();
        payload.setNo("414384432619753472");
         ResultModel response = client.createAttestationWithUrlConfirm(payload);
        Assert.assertNotNull(response.getData());

网页取证模式二 确认取证
------------------

::

        UrlAttestationStep2Param payload = new UrlAttestationStep2Param();
        payload.setNo("414384432619753472");
        ResultModel response = client.downloadImgWithUrlAttestation(payload);
        Assert.assertNotNull(response.getData());

获取保全数据
------------------

::

      ResultModel response = client.attestationInfo("434766956529192961");
      System.out.println(Utils.objectToJson(response));


下载保全文件
------------------

::

	try {
        DownloadAttestationInfo response = client.downloadFile("452886513210892289");
		FileOutputStream fileOutputStream = new FileOutputStream(downloadFile.getFileName());
		IOUtils.copy(downloadFile.getFile(), fileOutputStream);
		fileOutputStream.close();
	} catch (ServerException e) {
		System.out.println(e.getMessage());
	}

获取过程取证token
------------------

::

        ProcessAttestationParam payload = new ProcessAttestationParam();
        payload.setTemplate_id("mqAZQwNZbpbrmVTob6Ss");
        payload.setUnique_id(randomUniqueId());
        payload.setEvidenceType("PC");
        Map<IdentityTypeEnum, String> identities = new HashMap<IdentityTypeEnum, String>();
        identities.put(IdentityTypeEnum.ID, "15812383");
        payload.setIdentities(identities);
        List<PayloadFactoidParam> factoids = new ArrayList<PayloadFactoidParam>();
        PayloadFactoidParam factoid = new PayloadFactoidParam();
        factoid.setUnique_id(randomUniqueId());
        factoid.setType("evidence");
        LinkedHashMap<String, String> map = new LinkedHashMap<String, String>();
        factoid.setData(map);
        map.put("file_name", "李三");
        map.put("size", "37791");
        factoids.add(factoid);
        payload.setFactoids(factoids);
        ResultModel response = null;
        response = client.createProcessToken(payload);
        System.out.println(Utils.objectToJson(response));
        Assert.assertNotNull(response.getData());

查询过程取证详情
------------------

::

        ResultModel response  client.getProcessInfo("485470291179343873");
        System.out.println(Utils.objectToJson(response));

结束过程取证
------------------

::
        ResultModel response client.stopProcess("425043414342438912");
        System.out.println(Utils.objectToJson(response));




