# Lead_Automation
This code retrieves attachments associated with a specific record in Zoho CRM, converts the data contained within the attachments into lead records, and returns a list of the IDs of the newly created lead records.
<Head> Code Explaination </Head>

Created a Custom Button field in the property database module for attachemnt extraction in json format, to push into CRM

<img width="1372" alt="Screen Shot 2023-03-06 at 3 42 45 PM" src="https://user-images.githubusercontent.com/125079632/223226110-0ab44216-dcee-48f6-82de-6d9de797eec8.png">

<img width="1103" alt="Screen Shot 2023-03-06 at 3 56 14 PM" src="https://user-images.githubusercontent.com/125079632/223228741-aa5f3b4a-20e8-4a68-a096-346f000dfd84.png">

Created a Custom Function, for fetching the Property Database ID

<img width="758" alt="Screen Shot 2023-03-06 at 3 57 15 PM" src="https://user-images.githubusercontent.com/125079632/223228949-a9e6919d-a6c6-495b-b1d4-350e028d2c8d.png">

Deluge script explaination


get_attachments = zoho.crm.getRelatedRecords("Attachments","PropertyDatabase",get_id);: This line retrieves all the attachments related to a specific record in the "PropertyDatabase" module. The get_id variable should contain the ID of the record.

if(get_attachments.size() > 0): This line checks whether there are any attachments returned by the previous line.

for each r in get_attachments: This line starts a loop that iterates over each attachment.

get_file = invokeurl [...]: This line retrieves the actual file associated with the current attachment by making a HTTP GET request to the Zoho CRM API. The get_id and r.getJSON("id") variables are used to construct the URL of the API endpoint. The resulting file content is stored in a map called dummy_map.

replace_value = if(dummy_map.get("Dummy_Key").contains("\r\n"),"\r\n","\n"): This line replaces any occurrences of Windows-style line endings (\r\n) in the file content with Unix-style line endings (\n), and stores the result in replace_value.

convert_into_list = "[" + dummy_map.get("Dummy_Key").replaceAll(replace_value,"],[") + "]": This line converts the file content from a string of comma-separated values into a list of lists. Each sublist represents a row of data, with each item in the sublist representing a column. The resulting list of lists is stored in convert_into_list.

<img width="1181" alt="Screen Shot 2023-03-06 at 3 58 54 PM" src="https://user-images.githubusercontent.com/125079632/223229246-051c3e0a-8b0c-463f-9c4d-f2388754f65e.png">




for each r in convert_into_list.toList(): This line starts a loop that iterates over each row in convert_into_list.

if(x > 1): This line skips the first row of convert_into_list, which is assumed to contain column headings rather than data.

if(r.get(130) != "" && r.get(128) != "" && r.get(132) != "" && r.get(133) != ""): This line checks whether the current row contains valid data. If all four columns (130, 128, 132, and 133) are not empty, the code proceeds to create a new lead record in Zoho CRM.

insert_map = Map(): This line creates a new map called insert_map, which will be used to store the data for the new lead record.

insert_map.put(...): This line populates the insert_map map with data from the current row of convert_into_list. Each column in the row is mapped to a specific field in the Leads module of Zoho CRM.

resp_lead = zoho.crm.createRecord("Leads",insert_map): This line creates a new lead record in Zoho CRM using the data in insert_map. The resulting lead record is stored in resp_lead.

id_list.add(resp_lead.getJSON("id")): This line adds the ID of the newly created lead record to a list called id_list.

return id_list: This line returns the id_list to the calling function.


<img width="1148" alt="Screen Shot 2023-03-06 at 4 01 06 PM" src="https://user-images.githubusercontent.com/125079632/223229748-be1b5f26-8e3d-4a5f-830e-3af351852c1e.png">

<img width="891" alt="Screen Shot 2023-03-06 at 4 01 54 PM" src="https://user-images.githubusercontent.com/125079632/223229911-64736b95-ac47-4332-8ef1-5972ee3d1361.png">

Process Flow



![Automation Process Flow1](https://user-images.githubusercontent.com/125079632/223496417-819cace4-cbd5-485e-b64d-a2fd4c1347a9.png)



