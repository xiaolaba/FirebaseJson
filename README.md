# The Firebase Json Arduino library for ESP8266/ESP32.


Parse and edit JSON object directly with specified relative path as in Firebase v 2.2.2


This library supports any Arduino supported MCU, ESP8266 and ESP32 are recommended. 


## Tested Devices

 * ESP8266
 * ESP32


## Features


* **None recursive operations**

* **Parse and edit JSON object directly with specified relative path as used in Firebase**

* **Prettify JSON string**



## Dependencies


This library required **ESP8266 Core SDK version 2.4.0 and above or ESP32 Core SDK version 1.0.2 and above**.

For Arduino IDE, ESP8266/ESP32 Core SDK can be installed through **Boards Manager**. 

For PlatfoemIO IDE, ESP8266 Core SDK can be installed through **PIO Home** > **Platforms** > **Espressif 8266 or Espressif 32**.




## Installation


For Arduino IDE, click on **Clone or download** dropdown at the top of repository, select **Download ZIP** 

From Arduino IDE, goto menu **Sketch** -> **Include Library** -> **Add .ZIP Library...**.

Choose **FirebaseJson-master.zip** that previously downloaded.

Go to menu **Files** -> **Examples** -> **FirebaseJson-master** and choose one from examples.


For PlatformIO, create folder **"FirebaseJson"** in folder **"lib"** and save **[these files](https://github.com/mobizt/FirebaseJson/tree/master/src)** in there.






## Usages


FirebaseJson library is so simple to use as you read, store and update(edit) the JSON node in Firebase RTDB.

Since you declare the FirebaseJson (object) or FirebaseJsonArray, use the functions `setJsonData`, `add`, `set` and `remove`
to build or edit JSON object and use `get` to parse the node's contents. 

Defined the relative path of the specific node to `add`, `set`, `remove` and `get` functions to add, set, remove and get its contents.


Function `FirebaseJson.setJsonData` is to set the JSON string to JSON object.


Function `FirebaseJson.add` is used for add the new node with the contents e.g. String, Number (int and double), Boolean, Array and Object to the defined relative path.


Function `FirebaseJson.set` is used for edit, overwrite, create new (if not exist) node with contents e.g. String, Number (int and double), Boolean, Array and Object at the defined relative path.


Function `FirebaseJson.remove` is used for remove node and all its children contents at the defined relative path. 


Function `FirebaseJson.toString` is used for (pretty or plain) print out the JSON object as Arduino string (this function takes String param).


Functions `FirebaseJson.iteratorBegin`, `FirebaseJson.iteratorGet` and `FirebaseJson.iteratorEnd` are used for parse all JSON object contents as list which can be iterated with index.


Function `FirebaseJson.clear` is used for clear JSON object contents.


Function `FirebaseJsonArray.add` is used for adding the new contents e.g. String, Number (int and double), Boolean, Array and Object to JSON array.


Function `FirebaseJsonArray.set` is for edit, overwrite, create new (if not exist) contents e.g. String, Number (int and double), Boolean, Array and Object at the defined relative path or defined index of JSON array.



Function `FirebaseJsonArray.remove` is used for remove array's contents at the defined relative path or defined index of JSON array.



Function `FirebaseJsonArray.toString` is used for (pretty or plain) print out the JSON array object as Arduino string (this function takes String param).


Function `FirebaseJsonArray.clear` is used for clear JSON object contents.


To acquired the JSON object or JSON Array from FirebaseData object which returned from get, set, push operations, these following functions are required.

`FirebaseData.jsonObject`

`FirebaseData.jsonObjectPtr`

`FirebaseData.jsonArray` and

`FirebaseData.jsonArrayPtr`

Function `FirebaseData.jsonObject` and `FirebaseData.jsonObjectPtr` will provide FirebaseJson (object) and FirebaseJson pointer respectively.

Function `FirebaseData.jsonArray` and `FirebaseData.jsonArrayPtr` will provide FirebaseJson Array and FirebaseJson Array pointer respectively.



The following example show how to use FirebaseJson.

```C++
//Declare FirebaseJson object (global or local)
FirebaseJson json;

//Add key1 with value 100 to JSON object
json.add("name", "Living Room");

//Add temp with value 80 and unit with value Celcius to JSON object
//Note: temp2 is not the child of temp1 as in previous version.
json.add("temp1", 120).add("temp2", 40);

//To add nested child contents directly
json.set("unit/temp1", "Farenheit");
json.set("unit/temp2", "Celcius");

//To print out as prettify string
String jsonStr;
json.toString(jsonStr, true);
Serial.println(jsonStr);

/*
This is the result of above code

{
    "name": "Living Room",
    "temp1": 120,
    "temp2": 40,
    "unit": {
        "temp1": "Farenheit",
        "temp2": "Celcius"
    }
}
*/

//To set array to the above JSON using FirebaseJson directly
//Set (add) array indexes 0,1,2,5,7 under temp1
json.set("temp1/[0]", 47);
json.set("temp1/[1]", 28);
json.set("temp1/[2]", 34);
json.set("temp1/[5]", 23);
json.set("temp1/[7]", 25);

//Print out as prettify string
json.toString(jsonStr, true);
Serial.println(jsonStr);

/*
The result of above code

{
    "name": "Living Room",
    "temp1": [
        47,
        28,
        34,
        null,
        null,
        23,
        null,
        25
    ],
    "temp2": 40,
    "unit": {
        "temp2": "Celcius"
    }
}
*/

//Try to remove temp1 array at index 1
json.remove("temp1/[1]");

//Try to remove temp2
json.remove("temp2");

//Print out as prettify string
json.toString(jsonStr, true);
Serial.println(jsonStr);

/*
The result of above code

{
    "name": "Living Room",
    "temp1": [
        47,
        34,
        null,
        null,
        23,
        null,
        25
    ],
    "unit": {
        "temp1": "Farenheit",
        "temp2": "Celcius"
    }
}
*/

//Now parse/read the contents from specific node

FirebaseJsonData jsonData;

json.get(jsonData, "unit/temp2");

if(jsonData.success)
{
  //Type of parsed data
  Serial.println(jsonData.type);
  //Its value
  Serial.println(jsonData.stringValue);
  //Serial.println(jsonData.intValue);
  //Serial.println(jsonData.boolValue);
  //Serial.println(jsonData.doubleValue);

}



```


The following example show how to use FirebaseJsonArray.

```C++
//Declare FirebaseJsonArray object (global or local)
FirebaseJsonArray arr;

//Add some data
arr.add("banana");
arr.add("mango");
arr.add("coconut");


//Change the array contents
arr.set("[1]/food", "salad");
arr.set("[2]", "apple");
arr.set("[4]/[0]/[1]/amount", 20);

//To print out as prettify string
String arrStr;
arr.toString(arrStr, true);
Serial.println(arrStr);

/*
This is the result of above code

[
    "banana",
    {
        "food": "salad"
    },
    "apple",
    null,
    [
        [
            null,
            {
                "amount": 20
            }
        ]
    ]
]
*/

//Remove array content
arr.remove("[4]/[0]/[1]/amount");

//Print out as prettify string
arr.toString(arrStr, true);
Serial.println(arrStr);

/*
The result of above code

[
    "banana",
    {
        "food": "salad"
    },
    "apple",
    null,
    [
        [
            null
        ]
    ]
]
*/

//Now parse/read the array contents at some index

FirebaseJsonData jsonData;

arr.get(jsonData, "[1]/food");

if(jsonData.success)
{
  //Type of parsed data
  Serial.println(jsonData.type);
  //Its value
  Serial.println(jsonData.stringValue);
  //Serial.println(jsonData.intValue);
  //Serial.println(jsonData.boolValue);
  //Serial.println(jsonData.doubleValue);

}


```


## All Available Functions

### FirebaseJson object functions


#### Clear internal buffer of FirebaseJson object.
    
return **`instance of an object.`**

```C++
FirebaseJson &clear();
```






#### Set JSON data (JSON object string) to FirebaseJson object.
    
param **`data`** - The JSON object string.

return **`instance of an object.`**

```C++
FirebaseJson &setJsonData(const String &data);
```






#### Add null to FirebaseJson object.
    
param **`key`** - The new key string that null to be added.

return **`instance of an object.`**

```C++
FirebaseJson &add(const String &key);
```






#### Add string to FirebaseJson object.
    
param **`key`** - The new key string that string value to be addedd.

param **`value`** - The String value for new specified key.

return **`instance of an object.`**

```C++
FirebaseJson &add(const String &key, const String &value);
```






#### Add string (chars array) to FirebaseJson object.
    
param **`key`** - The new key string that string (chars array) value to be added.

param **`value`** - The char array for new specified key.

return **`instance of an object.`**

```C++
FirebaseJson &add(const String &key, const char *value);
```






#### Add integer/unsigned short to FirebaseJson object.
    
param **`key`** - The new key string that value to be added.

param **`value`** - The integer/unsigned short value for new specified key.

return **`instance of an object.`**

```C++
FirebaseJson &add(const String &key, int value);
FirebaseJson &add(const String &key, unsigned short value);
```






#### Add double to FirebaseJson object.
    
param **`key`** - The new key string that double value to be added.

param **`value`** - The double value for new specified key.

return **`instance of an object.`**

```C++
FirebaseJson &add(const String &key, double value);
```





#### Add boolean to FirebaseJson object.
    
param **`key`** - The new key string that bool value to be added.

param **`value`** - The boolean value for new specified key.

return **`instance of an object.`**

```C++
FirebaseJson &add(const String &key, bool value);
```





#### Add nested FirebaseJson object into FirebaseJson object.
    
param **`key`** - The new key string that FirebaseJson object to be added.

param **`json`** - The FirebaseJson object for new specified key.

return **`instance of an object.`**

```C++
FirebaseJson &add(const String &key, FirebaseJson &json);
```






#### Add nested FirebaseJsonArray object into FirebaseJson object.
    
param **`key`** - The new key string that FirebaseJsonArray object to be added.

param **`arr`** - The FirebaseJsonArray for new specified key.

return **`instance of an object.`**

```C++
FirebaseJson &add(const String &key, FirebaseJsonArray &arr);
```






#### Get the FirebaseJson object serialized string.

param **`buf`** - The returning String object.

param **`prettify`** - Boolean flag for return the pretty format string i.e. with text indentation and newline.. 


```C++
void toString(String &buf, bool prettify = false);
```






#### Get the value from specified node path in FirebaseJson object.

param **`jsonData`** - The returning FirebaseJsonData that hold the returned data.

param **`path`** - Relative path to the specific node in FirebaseJson object.

param **`prettify`** - The bool flag for prettify string in FirebaseJsonData's stringValue.

return **`boolean status of operation.`**

    The FirebaseJsonData object hold the returned data which can be read from the following properties.

    jsonData.stringValue - contains the returned string.

    jsonData.intValue - contains the returned integer value.

    jsonData.doubleValue - contains the returned double value.

    jsonData.boolValue - contains the returned boolean value.

    jsonData.success - used to determine the result of get operation.

    jsonData.type - used to determine the type of returned value in string represent 
    the types of value e.g. string, int, double, boolean, array, object, null and undefined.

    jsonData.typeNum used to determine the type of returned value in integer as represented by the following value.
    
    JSON_UNDEFINED = 0
    JSON_OBJECT = 1
    JSON_ARRAY = 2
    JSON_STRING = 3
    JSON_INT = 4
    JSON_DOUBLE = 5
    JSON_BOOL = 6 and
    JSON_NULL = 7

 
 ```C++
 bool get(FirebaseJsonData &jsonData, const String &path, bool prettify = false);
 ```






#### Parse and collect all node/array elements in FirebaseJson object.  

param **`data`** - The JSON data string to parse (optional for replace the internal buffer with new data).

return **`number`** of child/array elements in FirebaseJson object.

 ```C++
 size_t iteratorBegin(const char* data = NULL);
 ```






#### Get child/array elements from FirebaseJson objects at specified index.
    
param **`index`** - The element index to get.

param **`type`** - The integer which holds the type of data i.e. JSON_OBJECT and JSON_ARR

param **`key`** - The string which holds the key/name of object, can return empty String if the data type is array.

param **`value`** - The string which holds the value for the element key or array.   

 ```C++
 void iteratorGet(size_t index, int &type, String &key, String &value);
 ```





#### Clear all iterator buffer (should be called since iteratorBegin was called).

 ```C++
 void iteratorEnd();
 ```




#### Set null to FirebaseJson object at specified node path.
    
param **`path`** - The relative path that null to be set.


The relative path can be mixed with array index (number placed inside square brackets) and node names e.g. /myRoot/[2]/Sensor1/myData/[3].


```C++
void set(const String &path);
```





#### Set String value to FirebaseJson object at specified node path.
    
param **`path`** - The relative path that string value to be set.

param **`value`** - The string value to set.


The relative path can be mixed with array index (number placed inside square brackets) and node names 
e.g. /myRoot/[2]/Sensor1/myData/[3].

```C++
void set(const String &path, const String &value);
```





#### Set string (chars array) value to FirebaseJson object at specified node path.
    
param **`path`** - The relative path that string (chars array) to be set.

param **`value`** - The char array to set.

The relative path can be mixed with array index (number placed inside square brackets) and node names 
e.g. /myRoot/[2]/Sensor1/myData/[3].

```C++
void set(const String &path, const char *value);
```





#### Set integer/unsigned short value to FirebaseJson object at specified node path.
    
param **`path`** - The relative path that int value to be set.

param **`value`** - The integer/unsigned short value to set.

The relative path can be mixed with array index (number placed inside square brackets) and node names 
e.g. /myRoot/[2]/Sensor1/myData/[3].

```C++
void set(const String &path, int value);
void set(const String &path, unsigned short value);
```







#### Set double value to FirebaseJson object at specified node path.
    
param **`path`** - The relative path that double value to be set.

param **`value`** - The double value to set.

The relative path can be mixed with array index (number placed inside square brackets) and node names 
e.g. /myRoot/[2]/Sensor1/myData/[3].

```C++
void set(const String &path, double value);
```







#### Set boolean value to FirebaseJson object at specified node path.
    
param **`path`** - The relative path that bool value to be set.

param **`value`** - The boolean value to set.

The relative path can be mixed with array index (number placed inside square brackets) and node names 
e.g. /myRoot/[2]/Sensor1/myData/[3].

```C++
void set(const String &path, bool value);
```







#### Set nested FirebaseJson object to FirebaseJson object at specified node path.
    
param **`path`** - The relative path that nested FirebaseJson object to be set.

param **`json`** - The FirebaseJson object to set.

The relative path can be mixed with array index (number placed inside square brackets) and node names 
e.g. /myRoot/[2]/Sensor1/myData/[3].

 ```C++
void set(const String &path, FirebaseJson &json);
```







#### Set nested FirebaseJsonAtrray object to FirebaseJson object at specified node path.
    
param **`path`** - The relative path that nested FirebaseJsonAtrray object to be set.

param **`arr`** - The FirebaseJsonAtrray object to set.

The relative path can be mixed with array index (number placed inside square brackets) and node names 
e.g. /myRoot/[2]/Sensor1/myData/[3].

```C++
void set(const String &path, FirebaseJsonArray &arr);
```






#### Remove specified node and its content.

param **`path`** - The relative path to remove its contents/children.

return **`bool`** value represents the success operation.

```C++
bool remove(const String &path);
```





### FirebaseJsonArray object functions


#### Add null to FirebaseJsonArray object.

return **`instance of an object.`**

```C++
FirebaseJsonArray &add();
```






#### Add string to FirebaseJsonArray object.

param **`value`** - The String value to add.

return **`instance of an object.`**

```C++
FirebaseJsonArray &add(const String &value);
```






#### Add string (chars arrar) to FirebaseJsonArray object.

param **`value`** - The chars array to add.

return **`instance of an object.`**

```C++
FirebaseJsonArray &add(const char *value);
```





#### Add integer/unsigned short to FirebaseJsonArray object.

param **`value`** - The integer/unsigned short value to add.

return **`instance of an object.`**

```C++
FirebaseJsonArray &add(int value);
FirebaseJsonArray &add(unsigned short value);
```






#### Add double to FirebaseJsonArray object.

param **`value`** - The double value to add.

return **`instance of an object.`**

```C++
FirebaseJsonArray &add(double value);
```






#### Add boolean to FirebaseJsonArray object.

param **`value`** - The boolean value to add.

return **`instance of an object.`**

```C++
FirebaseJsonArray &add(bool value);
```






#### Add nested FirebaseJson object  to FirebaseJsonArray object.

param **`json`** - The FirebaseJson object to add.

return **`instance of an object.`**

```C++
FirebaseJsonArray &add(FirebaseJson &json);
```





#### Add nested FirebaseJsonArray object  to FirebaseJsonArray object.

param **`arr`** - The FirebaseJsonArray object to add.

return **`instance of an object.`**

```C++
FirebaseJsonArray &add(FirebaseJsonArray &arr);
```





#### Get the array value at specified index from FirebaseJsonArray object.

param **`jsonObj`** - The returning FirebaseJsonData object that holds data at specified index.

param **`index`** - Index of data in FirebaseJsonArray object.    

return **`boolean`** status of operation.

```C++
bool get(FirebaseJsonData &jsonObj, int index);
bool get(FirebaseJsonData *jsonData, int index);
```







#### Get the array value at specified path from FirebaseJsonArray object.

param **`jsonObj`** - The returning FirebaseJsonData object that holds data at specified path.

param **`path`** - Relative path to data in FirebaseJsonArray object.    

return **`boolean status of operation.`**

The relative path must be begin with array index (number placed inside square brackets) followed by 
other array indexes or node names e.g. /[2]/myData would get the data from myData key inside the array indexes 2.

```C++
bool get(FirebaseJsonData &jsonData, const String &path);
```






#### Get the length of array in FirebaseJsonArray object.  

return **`length of array.`**

```C++
size_t size();
```






#### Get the FirebaseJsonArray object serialized string.

param **`buf`** - The returning String object.

param **`prettify`** - Boolean flag for return the pretty format string i.e. with text indentation and newline. 


```C++
void toString(String &buf, bool prettify = false);
```






#### Clear all array in FirebaseJsonArray object.

return **`instance of an object.`**

```C++
FirebaseJsonArray &clear();
```







#### Set null to FirebaseJsonArray object at specified index.
    
param **`index`** - The array index to set null.

```C++
void set(int index);
```







#### Set String to FirebaseJsonArray object at specified index.
    
param **`index`** -The array index that String value to be set.

param **`value`** - The String to set.


```C++
void set(int index, const String &value);
```







#### Set string (chars array) to FirebaseJsonArray object at specified index.
    
param **`index`** - The array index that string (chars array) to be set.

param **`value`** - The char array to set.

```C++
void set(int index, const char *value);
```





#### Set integer/unsigned short value to FirebaseJsonArray object at specified index.
    
param **`index`** - The array index that int/unsigned short to be set.

param **`value`** - The integer/unsigned short value to set.

```C++
void set(int index, int value);
void set(int index, unsigned short value);
```






#### Set double value to FirebaseJsonArray object at specified index.
    
param **`index`** - The array index that double value to be set.

param **`value`** - The double value to set.

```C++
void set(int index, double value);
```






#### Set boolean value to FirebaseJsonArray object at specified index.
    
param **`index`** - The array index that bool value to be set.

param **`value`** - The boolean value to set.

```C++
void set(int index, bool value);
```






#### Set nested FirebaseJson object to FirebaseJsonArray object at specified index.
    
param **`index`** - The array index that nested FirebaseJson object to be set.

param **`value`** - The FirebaseJson object to set.

```C++
void set(int index, FirebaseJson &json);
```






#### Set nested FirebaseJsonArray object to FirebaseJsonArray object at specified index.
    
param **`index`** - The array index that nested FirebaseJsonArray object to be set.

param **`value`** - The FirebaseJsonArray object to set.

```C++
void set(int index, FirebaseJsonArray &arr);
```






    
#### Set null to FirebaseJson object at specified path.
    
param **`path`** - The relative path that null to be set.

The relative path must be begin with array index (number placed inside square brackets) followed by 
other array indexes or node names e.g. /[2]/myData would get the data from myData key inside the array indexes 2.

```C++
void set(const String &path);
```





#### Set String to FirebaseJsonArray object at specified path.
    
param **`path`** - The relative path that string value to be set.

param **`value`** - The String to set.

The relative path must be begin with array index (number placed inside square brackets) followed by 
other array indexes or node names e.g. /[2]/myData would get the data from myData key inside the array indexes 2.

```C++
void set(const String &path, const String &value);
```




#### Set string (chars array) to FirebaseJsonArray object at specified path.
    
param **`path`** - The relative path that string (chars array) value to be set.

param **`value`** - The char array to set.

The relative path must be begin with array index (number placed inside square brackets) followed by 
other array indexes or node names e.g. /[2]/myData would get the data from myData key inside the array indexes 2.

```C++
void set(const String &path, const char *value);
```





#### Set integer/unsigned short value to FirebaseJsonArray object at specified path.
    
param **`path`** - The relative path that integer/unsigned short value to be set.

param **`value`** - The integer value to set.

The relative path must be begin with array index (number placed inside square brackets) followed by 
other array indexes or node names e.g. /[2]/myData would get the data from myData key inside the array indexes 2.

```C++
void set(const String &path, int value);
void set(const String &path, unsigned short value);
```





#### Set double value to FirebaseJsonArray object at specified path.
    
param **`path`** - The relative path that double value to be set.

param **`value`** - The double to set.

The relative path must be begin with array index (number placed inside square brackets) followed by 
other array indexes or node names e.g. /[2]/myData would get the data from myData key inside the array indexes 2.

 ```C++
void set(const String &path, double value);
```





#### Set boolean value to FirebaseJsonArray object at specified path.
    
param **`path`** - The relative path that bool value to be set.

param **`value`** - The boolean value to set.

The relative path must be begin with array index (number placed inside square brackets) followed by 
other array indexes or node names e.g. /[2]/myData would get the data from myData key inside the array indexes 2.

```C++
void set(const String &path, bool value);
```





#### Set the nested FirebaseJson object to FirebaseJsonArray object at specified path.
    
param **`path`** - The relative path that nested FirebaseJson object to be set.

param **`value`** - The FirebaseJson object to set.

The relative path must be begin with array index (number placed inside square brackets) followed by 
other array indexes or node names e.g. /[2]/myData would get the data from myData key inside the array indexes 2.

```C++
void set(const String &path, FirebaseJson &json);
```






#### Set the nested FirebaseJsonArray object to FirebaseJsonArray object at specified path.
    
param **`path`** - The relative path that nested FirebaseJsonArray object to be set.

param **`value`** - The FirebaseJsonArray object to set.

The relative path must be begin with array index (number placed inside square brackets) followed by 
other array indexes or node names e.g. /[2]/myData would get the data from myData key inside the array indexes 2.

```C++
void set(const String &path, FirebaseJsonArray &arr);
```






#### Remove the array value at specified index from FirebaseJsonArray object.

param **`index`** - The array index to be removed.

return **`bool`** value represents the success operation.

```C++
bool remove(int index);
```






#### Remove the array value at specified path from FirebaseJsonArray object.

param **`path`** - The relative path to array in FirebaseJsonArray object to be removed.

return **`bool`** value represents the success operation.

The relative path must be begin with array index (number placed inside square brackets) followed by 
other array indexes or node names e.g. /[2]/myData would get the data from myData key inside the array indexes 2.

```C++
bool remove(const String &path);
```





### FirebaseJsonData object functions


#### Get array data as FirebaseJsonArray object from FirebaseJsonData object.
    
param **`jsonArray`** -The returning FirebaseJsonArray object.

return **`bool`** status for success operation.

This should call after pares or get function.

```C++
bool getArray(FirebaseJsonArray &jsonArray);
```





#### Get array data as FirebaseJson object from FirebaseJsonData object.
    
param **`jsonArray`** - The returning FirebaseJson object.

return **`bool`** status for success operation.

This should call after pares or get function.

```C++
bool getJSON(FirebaseJson &json);
```



### FirebaseJsonData object properties


**`stringValue`** The String value of parses data.

**`intValue`** The int value of parses data.

**`doubleValue`** The double value of parses data.

**`boolValue`** The bool value of parses data.

**`success`** used to determine the result of get operation.

**`type`** The type String of parses data e.g. string, int, double, boolean, array, object, null and undefined.

**`typeNum`** The type (number) of parses data in form of the following **`jsonDataType`** value.

**JSON_UNDEFINED = 0**

**JSON_OBJECT = 1**

**JSON_ARRAY = 2**

**JSON_STRING = 3**

**JSON_INT = 4**

**JSON_DOUBLE = 5**

**JSON_BOOL = 6 and**

**JSON_NULL = 7**

**`success`** The success flag of parsing data.



## License

The MIT License (MIT)

Copyright (c) 2019 K. Suwatchai (Mobizt)
Copyright (c) 2012–2018, Serge Zaitsev, zaitsev.serge@gmail.com


Permission is hereby granted, free of charge, to any person returning a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

