# Common errors in a form

FormShare manages your data better, but it does so by running more checks on your ODK Form. Though FormShare follows ODK's official standards, it has more restrictions. For example, if you have an ODK form with a variable called "repeat", it will not upload in FormShare or in Kobo because "repeat" is an ODK restricted word; however, if you have a "repeat" variable called "repeat\_of\_house\_hold\_members\_that\_live\_and\_eat\_in\_the\_household\_now," the ODK form will upload in Kobo but not in FormShare because such a name has more than 64 characters. **For this reason, if you have an ODK Form that works on other platforms, it might need some modifications to work with FormShare**. The platform will inform you about the error and how to correct it.

This section provides information about the common errors in a form

## The primary key does not exist

> Message from FromShare: "The primary key variable does not exist or is inside a repeat."

When you upload a form, FormShare asks you to indicate a variable to use to control duplicate submissions:

<figure><img src="../../.gitbook/assets/primary_key.png" alt=""><figcaption></figcaption></figure>

The variable that controls duplicate submissions must exist in your form and be OUTSIDE a repeat.

{% hint style="info" %}
**What is duplicate data?**

_Imagine you are surveying cattle in a rural village, and each animal has an ear tag. Two enumerators, James and Patricia, go around the village gathering the information for each animal. James and Patricia, without realizing it, survey the same animal (the same ear tag) and send the data. To solve this problem and prevent the same animal from appearing twice in our dataset, you can tell FormShare that the ear-tag variable **must be unique**. In this example, the **ear tag is the variable that you will use to control duplicated data**. FormShare will alert you to duplicated data, and you can correct it easily._
{% endhint %}

To fix this problem, select a different variable or move the variable that you want to use outside any "repeat".

## Duplicated variables

> Message from FromShare: The following variables are duplicated within repeats or outside repeats

The ODK standard allows having two or more variables with the same name as long as they are in different groups. FormShare does not allow that and will tell you which variables are repeated. Just rename the variables to fix the problem.

## Mixing coded and not coded languages <a href="#mixing_languages" id="mixing_languages"></a>

> Message from FromShare: This ODK form mixes coded and not coded languages. For example label::English (en) and label::Español. You need to code all the labels that are marked for translation.

FormShare can store variable and option descriptions in multiple languages. The [ODK standard for translating ODK](https://docs.getodk.org/form-language/) has evolved over time. In the beginning, ODK translations were done using, for example, label::Español. Currently, you add the [ISO 639-1 code](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes), for example, label::Español (es).

{% hint style="warning" %}
Please note that you need a space between the language description and its code. The standard is label::<mark style="color:blue;">\[Language description]</mark><mark style="color:red;">\[space]</mark><mark style="color:green;">(</mark><mark style="color:blue;">\[language code]</mark><mark style="color:green;">)</mark>
{% endhint %}

In FormShare, you cannot mix coded and not coded languages. To fix this problem, you need to code all the labels that are marked for translation; for example, change label::Español to label::Español (es)

## The variable to control duplicate submissions has an invalid type <a href="#invalid_primary_key" id="invalid_primary_key"></a>

> Message from FromShare: The variable to control duplicate submissions has an invalid type. For example, this variable cannot be note, picture, video, sound, select\_multiple, or geospatial. The most appropriate types are text, datetime, barcode, calculate, select\_one, or integer.

When you upload a form, FormShare asks you to indicate a variable to use to control duplicate submissions:

<figure><img src="../../.gitbook/assets/primary_key.png" alt=""><figcaption></figcaption></figure>

The variable used to control duplicate submissions cannot be a note, picture, video, sound, select\_multiple, or any geospatial type.

{% hint style="info" %}
**What is duplicate data?**

_Imagine you are surveying cattle in a rural village, and each animal has an ear tag. Two enumerators, James and Patricia, go around the village gathering the information for each animal. James and Patricia, without realizing it, survey the same animal (the same ear tag) and send the data. To solve this problem and prevent the same animal from appearing twice in our dataset, you can tell FormShare that the ear-tag variable **must be unique**. In this example, the **ear tag is the variable that you will use to control duplicated data**. FormShare will alert you to duplicated data, and you can correct it easily._
{% endhint %}

To fix this problem, select a different variable to control duplicate data.

## Tables with a name longer than 64 characters <a href="#invalid_table_name_size" id="invalid_table_name_size"></a>

> Message from FromShare: FormShare needs you to shorten the name of some of your tables. The following tables have a name longer than 64 characters...

FormShare stores submissions as relational data. This means that it creates tables with columns in a database to store your data as rows. This documentation includes a section explaining how FormShare stores your data; however, to explain this error, you need to understand when FormShare creates tables. FormShare creates tables in these three circumstances:

1. FormShare creates a table called "maintable" to store all variables outside any "repeat" structure.
2. Every "repeat" structure creates a table. The table name is the name of the repeat.
3. Every variable of type "selectˍmultiple" or "rank" creates a table to store each selection or each rank as independent rows. The name of the table will be <mark style="color:blue;">\[parentˍtable]</mark><mark style="color:red;">ˍmselˍ</mark><mark style="color:green;">\[variableˍname]</mark>. For example:

* A "selectˍmultiple" variable named "gender" outside any repeat will create the table called "<mark style="color:blue;">maintable</mark><mark style="color:red;">ˍmselˍ</mark><mark style="color:green;">gender</mark>".
* A "selectˍmultiple" variable named "livestock" inside a repeat named "livestock\_repeat" will create a table called "<mark style="color:blue;">livestockˍrepeat</mark><mark style="color:red;">ˍmselˍ</mark><mark style="color:green;">livestock</mark>".

FormShare uses [MySQL](https://en.wikipedia.org/wiki/MySQL) to store submissions as relational data, and MySQL restricts table names to a maximum of 64 characters. This error indicates that one or more tables have names longer than 64 characters. There are different ways that the error may happen:

{% embed url="https://docs.google.com/spreadsheets/d/1aCL8OneXBCtibU89GJx3l3igwSoNOm8WXsKBLu3kTRY/edit?usp=sharing" %}

To fix this error, rename the variables.

{% hint style="info" %}
It's best to use short variable names. FormShare has a data dictionary, so you can work with variable descriptions.
{% endhint %}

## CSV files with invalid characters in column headers <a href="#csv_files_invalid_columns" id="csv_files_invalid_columns"></a>

> Message from FromShare: The following CSV files have invalid characters in column headers

FormShare can read external CSV files and import their contents into the database. For example, FormShare will read the CSV file of variables like "selectˍoneˍfromˍfile" or "selectˍmultipleˍfromˍfile" and load the options into the database. The column headers must NOT contain any special characters like spaces or commas. Underscore (\_) is the only character that is allowed in column headers.

Fix this problem by replacing spaces with underscores and removing any other characters.

## CSV files with an invalid structure <a href="#invalid_csv_structure" id="invalid_csv_structure"></a>

> Message from FormShare: The following files have an invalid structure.

FormShare can read external CSV files and import their contents into the database. For example, FormShare will read the CSV file of variables like "selectˍoneˍfromˍfile" or "selectˍmultipleˍfromˍfile" and load the options into the database. This error happens when the CSV is corrupted, for example, when the file has 4 headers separated by a comma but a row has 5 values separated by a comma.

Fix this problem by checking the file in a CSV reader like MS Excel.

## Choice list with duplicate option <a href="#duplicate_option" id="duplicate_option"></a>

> Message from FormShare: The following options are duplicated in the ODK you just submitted

{% hint style="info" %}
**What are cascading choices?**

Cascading choices are sets of choice lists whose options depend on the selection of a previously selected option in another list. For example, your form may first ask the region a respondent is from, and then, in the next question, list only the towns in that region.

You can design your ODK so that the town code repeats in the town choice list; however, it will be unique within the context of a region. <mark style="color:red;">**This is not a good practice**</mark>.

To facilitate analysis, choice codes/names should be unique within a single choice list. If two choices in the same list share the same code/name, even if they are unique within the context of an earlier selected option, they will be harder to distinguish in analysis.
{% endhint %}

In an ODK, you can have cascading choices with duplicated options by marking the "allowˍchoiceduplicates" setting as true. However, FormShare does not allow duplicate options. **FormShare supports cascading choices,** but you must make each choice unique, regardless of context. Using the example above, you can make the town code unique by concatenating the region code and the town code: <mark style="color:blue;">\[regionˍcode]</mark><mark style="color:red;">**-**</mark><mark style="color:green;">\[townˍcode]</mark>.

{% hint style="info" %}
**Why does FormShare not allow duplicate options?**

FormShare [stores submissions as relational data](../repositories/how-does-formshare-stores-my-data.md). Choice lists are stored as lookup tables; for example, the choice list called "regions" will create the lookup table called "lkpˍregions". Each lookup table has a primary key; for example, the primary key of the lookup table "lkpˍregions" is "regionsˍcode". A **primary key must be unique,** and this is why FormShare does not allow duplicate options.
{% endhint %}

Fix this problem by making unique all codes/names within an option list.

## Malformed language <a href="#malformed_language" id="malformed_language"></a>

> Message from FormShare: Malformed language in your ODK. You have label:X (Y) when it must be label::X (Y). With two colons (::).

FormShare can store variable and option descriptions in multiple languages. The [ODK standard for translating an ODK](https://docs.getodk.org/form-language/) has evolved over time. In the beginning, ODK translations were done using, for example, label::Español. Currently, it is done by adding the [ISO 639-1 code](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes), for example, label::Español (es).

{% hint style="warning" %}
Please note that:

1. You need two colons (::) to indicate a translation. For example, label<mark style="color:purple;">**::**</mark>
2. You need a space between the language description and its code. The standard is label<mark style="color:purple;">**::**</mark><mark style="color:blue;">\[Language description]</mark><mark style="color:red;">\[space]</mark><mark style="color:green;">(</mark><mark style="color:blue;">\[language code]</mark><mark style="color:green;">)</mark>
{% endhint %}

FormShare is indicating that some of your translations have only one colon, for example, label<mark style="color:red;">**:**</mark>Español (es). Check your translations and fix the problem by adding a second colon.

## Choice list with names but not labels <a href="#names_but_no_labels" id="names_but_no_labels"></a>

> Message from FormShare: You have choice lists with names but not labels. Did you missed the :: between label and language? Like label<mark style="color:red;">**:**</mark>English (en)

FormShare can store variable and option descriptions in multiple languages. The [ODK standard for translating an ODK](https://docs.getodk.org/form-language/) has evolved over time. In the beginning, ODK translations were done using, for example, label::Español. Currently, it is done by adding the [ISO 639-1 code](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes), for example, label::Español (es).

{% hint style="warning" %}
Please note that:

1. You need two colons (::) to indicate a translation. For example, label<mark style="color:purple;">**::**</mark>
2. You need a space between the language description and its code. The standard is label<mark style="color:purple;">**::**</mark><mark style="color:blue;">\[Language description]</mark><mark style="color:red;">\[space]</mark><mark style="color:green;">(</mark><mark style="color:blue;">\[language code]</mark><mark style="color:green;">)</mark>
{% endhint %}

FormShare indicates that the "choices" sheet has a label with only one colon. For example, label<mark style="color:red;">**:**</mark>English (en). Check your labels in the "choices" sheet and fix the problem by adding a second colon.
