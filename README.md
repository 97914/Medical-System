# Medical-System: Page Object Model-Design Pattern
**Layer 1:Object Handler**
package objecthandler;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.ui.Select;

public class Reuse {
	WebDriver driver;
	public Reuse(WebDriver driver)
	{
		this.driver=driver;
	}
	public void enter(WebElement element,String value)
	{
		try {
	    element.sendKeys(value);
		}
		catch(Exception e)
		{
			System.out.println(e);
		}
	}
	public void click(WebElement element)
	{
		try {
		element.click();
		}
		catch(Exception e)
		{
			System.out.println(e);
		}
	}
	public void optiondropdown(WebElement element)
	{
		Select s= new Select(element);
		if(element.equals(s))
		{
		 s.selectByVisibleText("Second Option");
		}
		if(element.equals(s))
		{
			s.selectByValue("American Samoa");
		}
	}
}
**Layer 2: Pages**
**OpenMRS Login Page**

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;
import org.openqa.selenium.support.PageFactory;

public class Openmrs {
	public WebDriver driver;
	@FindBy(xpath="//input[@id='username']")
	public WebElement Log_Username;
	@FindBy(xpath="//input[@id='password']")
	public WebElement Log_Password;
	@FindBy(xpath="//li[@id='Registration Desk']")
	public WebElement Log_Selection;
	@FindBy(xpath="//input[@id='loginButton']")
	public WebElement Log_Confirm;
	
	public Openmrs(WebDriver driver)
	{
		this.driver=driver;
		PageFactory.initElements(driver, this);
		
	}
	
}
**Layer 3: Test **
Tests
package Test;

import org.testng.annotations.Test;

import Pages.Openmrs;
import Pages.contactform;
import objecthandler.Reuse;

import org.testng.annotations.BeforeClass;

import java.util.Properties;

import org.openqa.selenium.WebDriver;
import org.testng.annotations.AfterTest;

public class NewTest {
	WebDriver driver;
	Properties prop;
	TestBase base=new TestBase();
  @Test(priority=1)
  public void Login() {
	  String url=prop.getProperty("url");
	  driver.get(url);
	  Openmrs mrs=new Openmrs(driver);
	  /*mrs.Log_Username.sendKeys(prop.getProperty("username"));
	  mrs.Log_Password.sendKeys(prop.getProperty("password"));
	  mrs.Log_Selection.click();
	  mrs.Log_Confirm.click();*/
	  String username=prop.getProperty("username");
	  String password=prop.getProperty("password");
	  Reuse re=new Reuse(driver);
	  re.enter(mrs.Log_Username, username);
	  re.enter(mrs.Log_Password, password);
	  re.click(mrs.Log_Selection);
	  re.click(mrs.Log_Confirm);
  }
  @Test(priority=0)
  public void exam()
  {
	  String url=prop.getProperty("curl");
	  driver.get(url);
	  contactform cf=new contactform(driver);
	  Reuse re=new Reuse(driver);
	  re.optiondropdown(cf.optiondropdown);
	  re.enter(cf.attachfile,"C:\\Fixed1.jpg" );  
	  re.optiondropdown(cf.optiondropdown);
  }
  @BeforeClass
  public void beforeClass() {
	  driver=this.base.driver;
	  prop=this.base.prop;
	  driver.manage().window().maximize();
  }

  @AfterTest
  public void afterTest() throws InterruptedException {
	  Thread.sleep(2000);
	  //driver.close();
  }
}
**TestBase**
package Test;

import java.io.File;
import java.io.FileInputStream;
import java.util.Properties;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class TestBase {
	public static WebDriver driver;
	public static Properties prop;
	static
	{  
		try 
	{
		File f=new File("C:\\Java\\Exmaplepageobject\\ExamPO.properties");
		FileInputStream readfile=new FileInputStream(f);
		prop=new Properties();	
		prop.load(readfile);
	}
	catch(Exception e)
		{
		System.out.println(e);
		}
      String browsername="chrome";
      if(browsername.equalsIgnoreCase("chrome"))
      {
    	  System.setProperty("webdriver.chrome.driver","C:\\Selenium\\Driver\\chromedriver.exe");
    	  driver=new ChromeDriver();
      }
      else
      {
    	  System.out.println("Open FireFox");
      }	
	}

}
