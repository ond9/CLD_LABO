# LAB 02: PAAS - GOOGLE APP ENGINE

![img](img/title_img.png "logo")

Link to the full lab [here](https://cyberlearn.hes-so.ch/mod/assign/view.php?id=555052)

**Assignment from:** Laurent girod & Cyrill Zundler


## TASK 1: DEPLOYMENT OF A SIMPLE WEB APPLICATION
#### DELIVERABLE 1:

.java file :
the code of the project our project, can include servlets, business logic, Data entity, Storage service, etc.

web.xml :
 Determine how URLs map to servlets, which URLs require authentication, and other information.

appengine-web.xml:
Project proprieties for AppEngine services deployment, example our App ID.

## TASK 2: DEVELOP A SERVLET THAT USES THE DATASTORE
####  DELIVERABLE 2:

Here is the source of the servlet :

```java
package ch.heigvd.cld.lab;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.Enumeration;

import javax.servlet.ServletException;
import javax.servlet.http.*;

import com.google.appengine.api.datastore.DatastoreService;
import com.google.appengine.api.datastore.DatastoreServiceFactory;
import com.google.appengine.api.datastore.Entity;

@SuppressWarnings("serial")
public class DatastoreWriteServlet extends HttpServlet {
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp)
			throws ServletException, IOException {

		Entity entity;
		resp.setContentType("text/plain");
		PrintWriter pw = resp.getWriter();
		pw.println("These Data will be wrote to the datastore");

		/* get parameters from passed from the url */
		String kind = req.getParameter("_kind");
		String key  = req.getParameter("_key");

		/* check that at least _kind is passed trought the URL */
		if(kind == null){
			pw.println("No entity '_kind' in these targeted URL, nothing can be done");
			return;
		}

        /* We create the entity no matter _key is passed or not */
        if(key != null)
        	entity = new Entity(kind,key);
        else
        	entity = new Entity(kind);

        /* We iterate trought others params passed to the urls */
		String tmp;
		@SuppressWarnings("unchecked")
		Enumeration<String> listParam = req.getParameterNames();

        pw.println("kind = " + kind);
		pw.println("key = " + key);
		while(listParam.hasMoreElements()){

			tmp = listParam.nextElement().toString();

			/* we don't process again kind and key because they are processed before */
			if(tmp.compareTo("_kind") == 0 || tmp.compareTo("_key") == 0)
				continue;

			pw.println(tmp + " = " + req.getParameter(tmp));
			entity.setProperty(tmp, req.getParameter(tmp));
		}

		/* Write it to the datastore */
		DatastoreService datastore = DatastoreServiceFactory.getDatastoreService();
        datastore.put(entity);
	}
}
```
***Note:*** I put all in the same file entity, business logic, it's just for the assignment purpose... (if M. Liechti saw that...)

here is Local Datastore :  
![img](img/labo03_datastoreLocal.PNG "datastore")  

Here is Google Datastore:  
![img](img/labo03_datastoreOnline.PNG "datastore")

## TASK 3: TEST THE PERFORMANCE OF DATASTORE WRITES
####  DELIVERABLE 3:
