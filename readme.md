This is a clean java project with gradle build to start create your own project with Watosn.

## Learning objectives

This guide is for new developers who want to understand more about the Watson and learn how to developer Calorie Counter application using Watson services.

## Prerequisites

Before beginning this tutorial, you'll need:

* An IBM Cloud account: If you do not have an IBM Cloud account, you can create [here](https://bit.ly/2Hdkd4m).
* Install any IDE that can handle java on your computer (examples: Intellij, Visual Studio Code, Eclipse).

## Steps

1. Download git and open with IDE.
2. Create Watson Visual Recognition service.
3. Create Cloudant NoSQL service.
4. How to use Watson Visual Recognitoin service.
5. How to use Cloudant NoSQL service.
6. How to use Nutritionix API.

### 1. Download git and open with IDE:

Scroll up and press "Clone or Download" button and then "Download ZIP".

![Screenshot](https://github.com/tal2k4xj/Watson_Gradle_Clean/blob/master/images/downloadgit.png?raw=true)

Extract it where ever you will remember, and open it with the IDE (in this example with Visual Studio Code).

![Screenshot](https://github.com/tal2k4xj/Watson_Gradle_Clean/blob/master/images/visualstudiocode.png?raw=true)

### 2. Create Watson Visual Recognition service:

[Go To Visual Recognition sevice](https://cloud.ibm.com/catalog/services/visual-recognition), remember the service name and press "Create".

![Screenshot](https://github.com/tal2k4xj/Watson_Gradle_Clean/blob/master/images/visualrecognitioncreate.png?raw=true)

On the left menu press on "Manage" and save the Credentials (API Key and URL) on text file on your computer.

![Screenshot](https://github.com/tal2k4xj/Watson_Gradle_Clean/blob/master/images/visualreccreds.png?raw=true)

### 3. Create Cloudant NoSQL service:

[Go To Cloudant NoSQL sevice](https://cloud.ibm.com/catalog/services/cloudant), remember the service name, on "Available authentication methods" select "Use only IAM" and press "Create".

![Screenshot](https://github.com/tal2k4xj/Watson_Gradle_Clean/blob/master/images/createcloudant.png?raw=true)

On the left menu press on "Service credentials", click on "New credential" button (in the new window just press "Add") and now you can view your service credentials, save the Credentials on text file on your computer.

![Screenshot](https://github.com/tal2k4xj/Watson_Gradle_Clean/blob/master/images/cloudantcreds.png?raw=true)

### 4. How to use Watson Visual Recognitoin service:

Copy the code below to the Main.java, replace Your-Api-Key with the API-Key of Visual Recognition service that you copied before and Run the program.

	```Java
	import com.ibm.watson.developer_cloud.service.security.IamOptions;
	import com.ibm.watson.developer_cloud.visual_recognition.v3.*;
	import com.ibm.watson.developer_cloud.visual_recognition.v3.model.ClassifiedImages;
	import com.ibm.watson.developer_cloud.visual_recognition.v3.model.ClassifyOptions;
	import java.io.FileNotFoundException;
	import java.io.InputStream;
	import java.util.Arrays;

	public class Main {
		public static void main(String[] args) throws FileNotFoundException {
			IamOptions options = new IamOptions.Builder()
					.apiKey("Your-Api-Key")
					.build();

			VisualRecognition service = new VisualRecognition("2018-03-19", options);

			InputStream imagesStream = ClassLoader.getSystemClassLoader().getResourceAsStream("fruitbowl.jpg");
			ClassifyOptions classifyOptions = new ClassifyOptions.Builder()
					.imagesFile(imagesStream)
					.imagesFilename("fruitbowl.jpg")
					.owners(Arrays.asList("IBM"))
					.classifierIds(Arrays.asList("food"))
					.build();
			ClassifiedImages result = service.classify(classifyOptions).execute();
			System.out.println(result);
		}
	}
	```

For more information [Go To Watson Visual Recognition API Reference](https://cloud.ibm.com/apidocs/visual-recognition?language=java#classify-images).

![Screenshot](https://github.com/tal2k4xj/Watson_Gradle_Clean/blob/master/images/visualrecognitioncode.png?raw=true)

### 5. How to use Cloudant NoSQL service:

Copy the code below to the Main.java, replace Your-Api-Key & Your-URL with the API-Key & URL of Cloudant service that you copied before and Run the program.
	
	```Java
	import com.cloudant.client.api.ClientBuilder;
	import com.cloudant.client.api.CloudantClient;
	import com.cloudant.client.api.Database;
	import java.net.MalformedURLException;
	import java.net.URL;
	import java.util.List;

	public class Main {
		public static void main(String[] args) throws MalformedURLException {
			// Create a new CloudantClient instance
			CloudantClient client = ClientBuilder.url(new URL("Your-URL"))
					.iamApiKey("Your-Api-Key")
					.build();

			// Get a List of all the databases this Cloudant account
			List<String> databases = client.getAllDbs();
			System.out.println("All my databases : ");
			for ( String db : databases ) {
				System.out.println(db);
			}

			// Create a new database.
			client.createDB("example_db");

			// Get a Database instance to interact with, but don't create it if it doesn't already exist
			Database db = client.database("example_db", false);

			// Create an ExampleDocument and save it in the database
			db.save(new ExampleDocument(true));
			System.out.println("You have inserted the document");

			// Get an ExampleDocument out of the database and deserialize the JSON into a Java type
			ExampleDocument doc = db.find(ExampleDocument.class,"example_id");
			System.out.println(doc);
		}

		public static class ExampleDocument {
			private String _id = "example_id";
			private String _rev = null;
			private boolean isExample;

			public ExampleDocument(boolean isExample) {
				this.isExample = isExample;
			}

			public String toString() {
				return "{ id: " + _id + ",\nrev: " + _rev + ",\nisExample: " + isExample + "\n}";
			}
		}
	}
	```

For more information [Go To Cloudant NoSQL Java SDK Reference](https://github.com/cloudant/java-cloudant#getting-started).

![Screenshot](https://github.com/tal2k4xj/Watson_Gradle_Clean/blob/master/images/cloudantjavasdk.png?raw=true)

### 6. How to use Nutritionix API:

Copy the code below to the Main.java and Run the program.

	```Java
	import java.io.BufferedReader;
	import java.io.DataOutputStream;
	import java.io.IOException;
	import java.io.InputStreamReader;
	import java.net.HttpURLConnection;
	import java.net.URL;

	public class Main {
		public static void main(String[] args) throws IOException {
			String NUTRITIONIX_ID = "b14f5890";
			String NUTRITIONIX_KEY = "e94ac7afb24c1bfd1d52e43538cc8498";
			String NUTRITIONIX_USER = "tal2k4xj";

			//set up the parameters of our request
			String url = "https://trackapi.nutritionix.com/v2/natural/nutrients";

			URL obj = new URL(url);
			HttpURLConnection con = (HttpURLConnection) obj.openConnection();

			con.setRequestMethod("POST");

			con.setRequestProperty("User-Agent", "Mozilla/5.0");
			con.setRequestProperty("Content-Type", "application/json");
			con.setRequestProperty("x-app-id", NUTRITIONIX_ID);
			con.setRequestProperty("x-app-key", NUTRITIONIX_KEY);
			con.setRequestProperty("x-remote-user-id", NUTRITIONIX_USER);

			String urlParameters = "{\"query\":\"" + "Banana" + "\"}";

			//start the connection and send request
			con.setDoOutput(true);
			DataOutputStream wr = new DataOutputStream(con.getOutputStream());

			wr.writeBytes(urlParameters);

			wr.flush();
			wr.close();

			//get result
			con.getResponseCode();
			BufferedReader in = new BufferedReader(new InputStreamReader(con.getInputStream()));
			String inputLine;
			StringBuffer response = new StringBuffer();

			while ((inputLine = in.readLine()) != null) {
				response.append(inputLine);
			}
			in.close();

			System.out.println(response.toString());
		}
	}
	```

For more information [Go To Nutritionix API Live Demo](https://www.nutritionix.com/natural-demo).

![Screenshot](https://github.com/tal2k4xj/Watson_Gradle_Clean/blob/master/images/nutritionxlivedemo.png?raw=true)