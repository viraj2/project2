import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;

import java.io.File;
import java.io.InputStream;

import java.string.StringBuilder;

node('master'){
	def workSpaceHome = pwd()
    stage('Clean') {
        deleteDir()
    }
    stage('Checkout') {
        checkout scm
    }
	stage('Build'){
		if (isUnix()) {
			sh "'${mvnHome}/bin/mvn' clean test"
		} else {
			bat(/"${mvnHome}\bin\mvn" clean test/)
		}
	}
	stage('UploadResults'){
		load(workSpaceHome + "/config.groovy")
		echo "Uploading testresult file........"
		def baseurl=${baseUrl}
		def apikey=${apikey}
		def scope=${scope}
		def absolutefilepath=workSpaceHome+${filepath}
		def buildid=${buildId}
		def platformid=${platformId}
		def dropid=${dropId}
		def entitytype=${entityType}
		def fileid
		
		echo "baseurl:"+baseurl
		echo "apikey:"+apikey
		echo "scope:"+scope
		echo "absolutepath:"+absolutefilepath
		echo "buildID:"+buildid
		echo "platformID:"+platformid
		echo "dropID:"+dropid
		echo "entityType:"+entitytype
		
		//Uploading file
		/*CloseableHttpClient httpClient = HttpClients.createDefault();
		HttpPost uploadFile=new HttpPost(baseurl+"/rest/import/create/1");
		uploadFile.addHeader("Accept","application/json");
		uploadFile.addHeader("apiKey",apikey);
		uploadFile.addHeader("scope",scope);
		MultipartEntityBuilder builder = MultipartEntityBuilder.create();
		File f = new File(absolutefilepath);
		builder.addPart("file", new FileBody(f));
		HttpEntity multipart = builder.build();
		uploadFile.setEntity(multipart);
		CloseableHttpResponse response = httpClient.execute(uploadFile);
		int code=response.getStatusLine().getStatusCode();
		if(code!=200)
		{
			echo "StatusCode:"+code
		}
		else
		{
			HttpEntity entity = response.getEntity();
			if(entity!=null)
			{
				InputStream content = entity.getContent();
				StringBuilder  builder1 = new StringBuilder();
				Reader read = new InputStreamReader(content, StandardCharsets.UTF_8);
				BufferedReader reader = new BufferedReader(read);
				String line;
				try {
					while ((line = reader.readLine()) != null) {
						builder1.append(line);
					}

				}
				finally{
					reader.close();
					content.close();
				}
				JSONParser parser=new JSONParser();
				JSONObject responsejson=(JSONObject)parser.parse(builder1.toString());
				JSONObject data=(JSONObject)responsejson.get("data");
				JSONObject dataarray=(JSONObject)data.get(0);
				fileid=(String)dataarray.get("id");
				echo fileid

			}
		}
		
		//Schedule a result import operation
		HttpPost schedule=new HttpPost(baseurl+"/rest/import/scheduler/results");
		schedule.addHeader("Accept","application/json");
		schedule.addHeader("Content-Type","application/json");
		schedule.addHeader("apiKey",apikey);
		schedule.addHeader("scope",scope);
		
		JSONObject jsonbody=new JSONObject();
		jsonbody.put("buildID",);
		jsonbody.put("platformID",);
		jsonbody.put("dropID",);
		jsonbody.put("importFileId",);
		jsonbody.put("entityType",);
		
		schedule.setEntity(jsonbody.toString());
		CloseableHttpResponse scheduleres=httpClient.execute(schedule);
		int code=scheduleres.getStatusLine().getStatusCode();*/
	}
}