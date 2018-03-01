import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.entity.mime.content.FileBody;
import org.apache.http.util.EntityUtils;
import org.apache.http.entity.StringEntity;

import java.io.File;
import java.io.InputStream;

import java.nio.charset.StandardCharsets;

import java.lang.StringBuilder;

import org.json.*;

node('master'){
	def workSpaceHome = pwd()
	
    stage('Clean') {
        deleteDir()
    }
	
    stage('Checkout') {
        checkout scm
    }
	stage('Build') {
     mvn test     
    }
	stage('UploadResults'){
		load(workSpaceHome + "/config.groovy")
		echo "Uploading Test Result File.........."
		String baseurl="${baseUrl}"
		String apikey="${apikey}"
		String absolutefilepath="${workSpaceHome}"+"/"+"${filepath}"
		String projectid="${projectId}"
		String releaseid="${releaseId}"
		String cycleid="${cycleId}"
		String platformid="${platformId}"
		String dropid="${dropId}"
		String entitytype="${entityType}"
		String scope=projectid+":"+releaseid+":"+cycleid
		
		echo "baseurl:"+baseurl
		echo "apikey:"+apikey
		echo "projectId:"+projectid
		echo "releaseId:"+releaseid
		echo "cycleId:"+cycleid
		echo "scope:"+scope
		echo "absolutePath:"+absolutefilepath
		echo "platformID:"+platformid
		echo "dropID:"+dropid
		echo "entityType:"+entitytype
		
		if(absolutefilepath.endsWith("*.xml") || absolutefilepath.endsWith("*.json"))
		{
			List<String> filelist=fetchFiles(absolutefilepath,entitytype);
			if(filelist!=null)
			{
				for(String f2:filelist)
				{
					uploadResults(baseurl,apikey,scope,f2,cycleid,platformid,dropid,entitytype);
				}
			}
		}
		else
		{
			uploadResults(baseurl,apikey,scope,absolutefilepath,cycleid,platformid,dropid,entitytype);
		}
		
		
	}
}
def public List<String> fetchFiles(String filePath,String entityType)
{
	List<String> list=new ArrayList<String>();
	String extention="";
	if(filePath.endsWith(".xml") &&(entityType.equals("JUNIT") || entityType.equals("TESTNG")))
	{
		extention=".xml";
	}
	else if(filePath.endsWith(".json") && entityType.equals("CUCUMBER"))
	{
		extention=".json";
	}
	else
	{
		echo "Enter valid filepath or entityType"
		return null;
	}
				
	File file=new File(filePath);
	
	File[] farray=file.getParentFile().listFiles();
	String path;
				
	if(farray==null)
	{
		echo "Can not find files specified"
		return null;
	}
	for(File f:farray)
	{
		path=f.getPath();
		if(path.endsWith(extention))
		{
			list.add(path);
		}
	}
	if(list.isEmpty())
	{
		echo "Can not find files specified"
		return null;
	}
	return list;
}
		
def public void uploadResults(String baseurl,String apikey,String scope,String absFilePath,String cycleid,String platformid,String dropid,String entitytype)
{
	//Uploading file
	echo "Uploading file "+absFilePath+".........."
	echo "Calling Upload Test Results API.........."
	CloseableHttpClient httpClient = HttpClients.createDefault();
			
	HttpPost uploadFile=new HttpPost(baseurl+"/rest/import/create/1");
			
	uploadFile.addHeader("Accept","application/json");
	uploadFile.addHeader("apiKey",apikey);
	uploadFile.addHeader("scope",scope);
			
	MultipartEntityBuilder builder = MultipartEntityBuilder.create();
	File f = new File(absFilePath);
			
	builder.addPart("file", new FileBody(f));
			
	HttpEntity multipart = builder.build();
			
	uploadFile.setEntity(multipart);
			
	CloseableHttpResponse response = httpClient.execute(uploadFile);
			
	int code=response.getStatusLine().getStatusCode();
	if(code!=200)
	{
		echo "Status Code:"+code
		echo "[ERROR]Problem in Uploading File"
		HttpEntity entity = response.getEntity();
		if(entity!=null)
		{
			String jsonString = EntityUtils.toString(entity);
			echo "Error response:"+jsonString
		}
	}
	else
	{
		echo "Status Code:"+code
		HttpEntity entity = response.getEntity();
		if(entity!=null)
		{
			String jsonString = EntityUtils.toString(entity);
			echo "Response of Upload Test Results API:"+jsonString
					
			JSONObject responsejson = new JSONObject(jsonString);
			JSONArray dataarray=responsejson.getJSONArray("data");
			JSONObject data=dataarray.getJSONObject(0);
			String fileid=(String)data.get("id");
			echo "File Id:"+fileid
					
			//Schedule a result import operation
			echo "Calling Schedule Result Import API.........."
			HttpPost schedule=new HttpPost(baseurl+"/rest/import/scheduler/results");
			schedule.addHeader("Accept","application/json");
			schedule.addHeader("Content-Type","application/json");
			schedule.addHeader("apiKey",apikey);
			schedule.addHeader("scope",scope);
			String body="{\"buildID\":"+cycleid+",\"platformID\":"+platformid+",\"dropID\":"+dropid+",\"importFileId\":"+fileid+",\"entityType\":"+"\""+entitytype+"\"}"
			StringEntity params =new StringEntity(body);
			schedule.setEntity(params);
			CloseableHttpResponse scheduleres=httpClient.execute(schedule);
			int fcode=scheduleres.getStatusLine().getStatusCode();
			echo "Status Code:"+fcode
			if(code==200)
			{
				String finalresponse= EntityUtils.toString(scheduleres.getEntity());
				echo "Response of Schedule Result Import API:"+finalresponse
			}
			else
			{
				echo "[ERROR]Problem in Schedule Result Import"
				if(scheduleres.getEntity()!=null)
				{
					String finalresponse= EntityUtils.toString(scheduleres.getEntity());
					echo "Error response:"+finalresponse
				}
			}
		}
	}
}