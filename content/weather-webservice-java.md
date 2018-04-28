---
title: "Java调用天气Webservice的小应用"
date: 2015-05-27T23:03:50+08:00
---

废话不多说,直接贴代码：

CityReq.java

```java
package com.weather;

import javax.xml.bind.annotation.XmlElement;
import javax.xml.bind.annotation.XmlRootElement;

@XmlRootElement(name="getWeatherbyCityName",namespace="http://WebXml.com.cn/")
public class CityReq {

    private String theCityName;

    public String getTheCityName() {
        return theCityName;
    }

    @XmlElement(name="theCityName",namespace="http://WebXml.com.cn/")
    public void setTheCityName(String theCityName) {
        this.theCityName = theCityName;
    }

    
}
```

WeatherWebServiceTest.java

```java
package com.weather;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.HttpURLConnection;
import java.net.URL;

import javax.xml.bind.JAXBContext;
import javax.xml.bind.Marshaller;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.soap.MessageFactory;
import javax.xml.soap.SOAPBody;
import javax.xml.soap.SOAPConstants;
import javax.xml.soap.SOAPEnvelope;
import javax.xml.soap.SOAPMessage;

import org.w3c.dom.Document;
public class WeatherWebServiceTest {

    public static void main(String[] args) {
        // TODO Auto-generated method stub
        weather();
    }
    static void weather(){
        System.out.println("开始登陆...");
        String wsdl="http://www.webxml.com.cn/WebServices/WeatherWebService.asmx?wsdl";
        System.out.println("wsdl:"+wsdl);
        HttpURLConnection urlconn=null;
        InputStream ins=null;
        OutputStream ous=null;
        try {
            URL u=new URL(wsdl);
            urlconn=(HttpURLConnection)u.openConnection();
            urlconn.setDoOutput(true);
            urlconn.setRequestMethod("POST");
            urlconn.setRequestProperty("Content-Type", "application/soap+xml; charset=utf-8");
            //urlconn.setRequestProperty("Content-Type", "text/xml; charset=utf-8");
            
            //发送数据
            ous=urlconn.getOutputStream();
            
            
            Document document=DocumentBuilderFactory.newInstance().newDocumentBuilder().newDocument();
            //编组
            Marshaller marsh=JAXBContext.newInstance(CityReq.class).createMarshaller();
            CityReq xmlf=new CityReq();
            xmlf.setTheCityName("北京");
            //JAXB.marshal(xmlf, new PrintWriter(System.out));
            marsh.marshal(xmlf, document);
            //创建soapmessage对象
            SOAPMessage soapMessage=MessageFactory.newInstance(SOAPConstants.SOAP_1_2_PROTOCOL).createMessage();
            SOAPBody soapBody=soapMessage.getSOAPBody();
            soapBody.addDocument(document);
            SOAPEnvelope soapEnvelope = soapMessage.getSOAPPart().getEnvelope();
            soapEnvelope.removeNamespaceDeclaration("env");
            soapEnvelope.addNamespaceDeclaration("soap12", "http://www.w3.org/2003/05/soap-envelope");
            soapEnvelope.addNamespaceDeclaration("xsi", "http://www.w3.org/2001/XMLSchema-instance");
            soapEnvelope.addNamespaceDeclaration("xsd", "http://www.w3.org/2001/XMLSchema");
            soapEnvelope.setPrefix("soap12");
            soapEnvelope.removeChild(soapEnvelope.getHeader());
            soapBody.setPrefix("soap12");
            //发送数据
           soapMessage.writeTo(ous);
           // soapMessage.writeTo(System.out);
            System.out.println(urlconn.getResponseCode());
            System.out.println(urlconn.getResponseMessage());
            //接收数据
            ins=urlconn.getInputStream();
            //接收的数据需要解组?
           StringBuffer respMsg=new StringBuffer();
            byte[] bytes=new byte[1024*1024];
            int a=-1;
            while ((a=ins.read(bytes))!=-1) {
                respMsg.append(new String(bytes,0,a));
            }
            System.out.println(respMsg.length());
            System.out.println(respMsg);
            
            //解组的方式
          /* SOAPMessage responseMessage=MessageFactory.newInstance(SOAPConstants.SOAP_1_2_PROTOCOL).createMessage(null, ins);
            Unmarshaller unmarsh=JAXBContext.newInstance(CityResp.class).createUnmarshaller();
            JAXBElement<CityResp> reponse= unmarsh.unmarshal(responseMessage.getSOAPBody().extractContentAsDocument(), CityResp.class);
            CityResp uresp= reponse.getValue();
            System.out.println(uresp.getResult());*/
            
            ous.close();
            ins.close();
            urlconn.disconnect();
        } catch (Exception e) {
            e.printStackTrace();
        }finally{
            
        }
    }
    
     
}
```

 