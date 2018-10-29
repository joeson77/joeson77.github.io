package com.sinosig.ec.ws.util;

import java.util.Scanner;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import com.sinosig.ec.ws.common.util.GetResponseXmlUtil;
import com.sinosig.ec.ws.common.util.JaxbConverter;
import com.sinosig.ec.ws.endpoint.agent.dto.RegistDTO;
import com.sinosig.ec.ws.exception.JaxbConvertException;

public class ThreadUtils {
	
	//创建线程池
	private final static ExecutorService executor = Executors.newCachedThreadPool();
	
	public static void main(String[] args) {
		
		//创建一个发令官计数器
		final CountDownLatch order = new CountDownLatch(1);
		//创建100个压测线程计数器
//		final CountDownLatch pressureCount = new CountDownLatch(200);
		//循环启动100个线程
		for (int i = 0; i < 200; i++) {
			Runnable runnable = new Runnable() {
				
				public void run() {
					try {
						System.out.println("线程" + Thread.currentThread().getName() + "已创建。。");
//						String enStrZ = Test.encrypt(
//								"{\"sysNo\":\"D8444799F613ED5092E4F7520A3A0088\","
//								+ "\"accountId\":\"15993272007\","
//								+ "\"pwd\":\"a123456\","
//								+ "\"accType\":\"02\","
//								+ "\"loginIp\":\"127.0.0.1\","
//								+ "\"signFlag\":\"N\","
//								+ "\"sysPwd\":\"312D8B68894FDCA579EE037CF22BCA12\"}",
//								"ygbx456&");
//						System.out.println("enStr=" + enStrZ);
						
						String WS_URL = "http://10.200.3.37:7002/ws-sinosig/ws/lifeAppService?wsdl";
						
						String WS_NAMESPACE = "http://ws.sinosig.com/ws";
						
						String method = "lifeAppRegist";
						
						String WS_USERNAME = "74A95A1FC05ECCD6FB272FBC273D2B41";
						
						String WS_USERPWD = "7CF8FB5FD488B6A714B866496923321C";
						
						//registAndActivate
						RegistDTO registDTO2 = new RegistDTO();
						registDTO2.setCUSTNAME("张三");
						registDTO2.setIDENTITYTYPE("1");
						registDTO2.setIDENTITYNO("110101198001010097");
						registDTO2.setCUSTMOBILE("18611989423");
						registDTO2.setCUSTPHONE("010-59939381");
						registDTO2.setCUSTGENDER("1");
						registDTO2.setCUSTEMAIL("huangqw2012@163.com");
						registDTO2.setCUSTBIRTH("1983-03-04");
						registDTO2.setPROVINCECODE("11");
						registDTO2.setREGIONCODE("110000");
						registDTO2.setADDRESS("花园北路");
						registDTO2.setPOSTALCODE("100091");
						registDTO2.setACCOUNTNAME("18611989423");
						registDTO2.setACCOUNTTYPE("02");
						registDTO2.setACCOUNTPWD("123abc");
						registDTO2.setCHANNELTYPE("10");
						registDTO2.setSOURCETYPE("01");
						
						String requestXml = "<ECOMMERCE> <ACCOUNTNAME>18611989443</ACCOUNTNAME> <ACCOUNTPWD>app123456</ACCOUNTPWD> <CUSTNAME>测试</CUSTNAME> <IDENTITYTYPE>1</IDENTITYTYPE> <IDENTITYNO>14222819740223489X</IDENTITYNO> <CUSTGENDER>1</CUSTGENDER> <CUSTBIRTH>1974-02-23</CUSTBIRTH> <CHANNELTYPE>30</CHANNELTYPE> <SOURCETYPE>01</SOURCETYPE> <AGENTREGISTTYPE>1</AGENTREGISTTYPE> <AGENTINSTTYPE>03</AGENTINSTTYPE> <AGENTPLATFORM>08</AGENTPLATFORM> <AGENTREGISTSCENE>01</AGENTREGISTSCENE> <AGENTREGISTDESC>寿险APP用户注册</AGENTREGISTDESC></ECOMMERCE>";
						
//						try {
//							requestXml = JaxbConverter.objToXml(registDTO2);
//						} catch (JaxbConvertException e) {
//							e.printStackTrace();
//						}
						Object[] parameters = {WS_USERNAME, WS_USERPWD, requestXml};
						
						//命令线程等待执行指令
						order.await();
						System.out.println("线程" + Thread.currentThread().getName() +  "已接受启动命令。。");
						
						try {
							String xmlStr = GetResponseXmlUtil.getResponseXml(WS_URL, WS_NAMESPACE, method, parameters);
							System.out.println("返回报文: " + xmlStr);
						} catch (Exception e) {
							e.printStackTrace();
						}
						
//						long startTime = System.currentTimeMillis();
//						String returnStr = Test.submitByPost("http://10.200.3.37:7003/rest/face/login", 
//								enStrZ,
//								"D8444799F613ED5092E4F7520A3A0088");
////						long endTime = System.currentTimeMillis();
//						String decryptStr = Test.decrypt(returnStr,"ygbx456&");
//						System.out.println("返回的报文为 : " + decryptStr);
////						System.out.println("程序运行时间：" + (endTime - startTime) + "ms");
						
					} catch (InterruptedException e) {
						e.printStackTrace();
					} 
				}
			};
			executor.execute(runnable);
		}
		try {
			Thread.sleep(3000);
			System.out.println();
			System.out.print("请输入 Y 发送执行指令: ");
			Scanner scanner = new Scanner(System.in);
			String flag = scanner.nextLine();
			if ("Y".equals(flag)) {
				order.countDown();
				System.out.println("开始执行所有线程。。");
			}
		} catch (InterruptedException e) {
			e.printStackTrace();
		} finally {
			//任务结束，停止线程池的所有线程
			executor.shutdown(); 
		}
		
	}
	
}
