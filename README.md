# intellipy

intellipy provides an efficient way to execute Python scripts from within JAVA. It includes intellipy and java client. 

  - intellipy is a TCP/IP server used to execute dynamic python scripts.
  - The Java Client provides a java interface to connect with intellipy server and execute the desired Python script from within Java program.

# Configuration
intellipy can be mainly started in 2 ways :
1. Through shell/terminal
	 1. Locate where intellipy is installed on your python env, in the root dir of intellipy, open runpyserve.py and provide path of configuration file in the constructor of intellipy
    2. Run startintellipy.bat(for Windows)/startintellipy.sh(for UNIX) placed in the bin* directory to start the server. 
    3. Run stopintellipy.bat(for Windows)/stopintellipy.sh(for UNIX) placed in the bin* directory to stop the server. 
    
    ```sh
    * bin directory should be found on root directory of intellipy, on the location all libraries are being installed in your python env. 
    (Something like : path-to-python-env/lib/python3.5/site-packages/intellipy )
    ```
2. Through Python Console
	1. Passing address of configuration file for intellipy

	```python
	from intellipy.executor import IntelliPy
	intelliPy = IntelliPy("Path/To/Configuration/File/IntelliPy.Properties")
	intellipy.start()
	```
	2. Passing address tuple to the constructor of IntelliPy 

	```python
	from intellipy.executor import IntelliPy
	intelliPy = IntelliPy(("localhost", 45460))
	intelliPy.start()
	```
# Configuration File

| Key | Description | Default Value 
| ------ | ------ | -----
| port  | Port on which the IntelliPy should run | 45460
| log_level   | DEBUG/ERROR/INFO | DEBUG
| log_path   | absolute path of the directory on which the logs should be generated | 
| max_clients   | Number of clients that can simultaneously send requests on intellipy for execution  | 10
| request_timeout   | Time in seconds socket operations will raise a timeout exception if the timeout period value has elapsed before the operation has completed |600
|flake8_errors_to_consider| Error to be considered as blocking as per flake8 documentation (http://flake8.pycqa.org/en/latest/user/error-codes.html)| E101,E112,E113,E9,F8
|encryption| JSON Object containing encryption related attributes and their values |
|to_encrypt| Boolean flag to signify if script will be received in encrypted form | False
|key| AES encryption key (should be of size 16) | 
|iv| AES initialization vector (should be of size 16)| 
| load_models_while_startup    | Boolean flag to signify if some trained models should be loaded on startup of intellipy. | False
| model_load_info    | Should come into consideration only if load_models_while_startup is True. This is an Array of JSON Objects containing information of locations where models are stored. There can be various directories where models can be stored. In intellipy models in each directory should be serialized using only one serializing module. | 
| load_model_dir    | Absolute path of the directory where models are stored.  | 
| load_model_names    | If not all models are to be loaded and only specified models should be loaded on startup then mention file names here.  | 
| load_all_models    | Boolean flag signifies if all models are only specified models should be loaded.  | 
| module_name    | Name of the module used to serialize models onto disk | 
| import_syntax    | Python syntax for importing the module which was used to serialize models onto disk | 



### Example configuration structure

    {
    	"port": 45460,
    	"log_level": "DEBUG",
    	"log_path": "path/to/logs",
    	"max_clients" : 10,
    	"request_timeout": 600,
    	"flake8_errors_to_consider": "E101,E112,E113,E9,F8",
    	"encryption" : {
	   		"to_encrypt": "False",
	   		"key": "",
	   		"iv": ""
	   	},
    	"load_models_while_startup": "True",
    	"model_load_info": [{
    		"load_model_dir": "path/to/models",
    		"load_model_names": [
    			"lg",
    			"lg2"
    		],
    		"load_all_models": "False",
    		"module_name": "pickle",
    		"import_syntax": "import pickle as pickle"
    	}]
    }


# Getting started

### Terminal

1. Start Server
    1. Windows

    ```sh
    > startintellipy.bat
    ```
    2. Unix

    ```sh
    $ ./startintellipy.sh
    ```
2. Stop Server
	1. Windows
    ```sh
    > stopintellipy.bat
    ```
	2. Unix
    ```sh
    $ stopintellipy.sh
    ```

### Python Console

```python
>>> from intellipy.executor import IntelliPy
>>> intelliPy = IntelliPy("Path/To/Configuration/File/IntelliPy.Properties")
>>> intelliPy.start()

```
OR
```python
>>> from intellipy.executor import IntelliPy
>>> intelliPy = IntelliPy(("localhost", 45460))
>>> intelliPy.start()
```

### Java Interface

```java
import com.intellicus.intellipyjclient.PyExecutorException;
import com.intellicus.intellipyjclient.PyScriptExecutor;
import com.intellicus.intellipyjclient.PyScriptResult;

public class IntelliPyConnect {

	public static void main(String[] args) throws PyExecutorException {

		// Create an object of PyScriptExecutor, pass IP, port on which intellipy is running
		PyScriptExecutor pyExecutor = new PyScriptExecutor("127.0.0.1", 45460);

		String script = "import pandas as pd\n"
				+ "data = pd.read_csv(\'Train.csv\')\n"
				+ "from sklearn.preprocessing import Imputer\n"
				+ "imputer = Imputer(missing_values = \'NaN\', strategy = \'mean\', axis = 0)\n"
				+ "imputer = imputer.fit(data)\n"
				+ "treatedDataNumpy = imputer.transform(data)\n"
				+ "treatedData = pd.DataFrame(treatedDataNumpy, index=None, columns=None)\n"
				+ "treatedData.to_csv(\'TrainTreated.csv\', encoding='utf-8', index=False)\n";

		// send the script to intellipy, get the execution result
		PyScriptResult res = pyExecutor.executeScript(script);

		// check if the execution is success
		if (res.isSuccess()) {
			System.out.println("Script execution successful");
		} else {
			System.out.println("Execute python script failed: " + res.getMsg());
		}

	}
}

  
```

License
----

Apache License 2.0


**Free Software, Hell Yeah!**