# Quick Start



{% hint style="info" %}
myinfo-connector-python is built with Django to make easy-to-go application.&#x20;
{% endhint %}

## Step 1: Clone the repository in your local

{% tabs %}
{% tab title="Bash" %}
```
git clone git@github.com:leeleelee3264/myinfo-connector-python.git
```
{% endtab %}
{% endtabs %}

## Step 2: Install Pre-requisite

#### Install Python&#x20;

{% hint style="info" %}
You can exclude python 3 is already installed in your local.
{% endhint %}

{% tabs %}
{% tab title="Bash" %}
```
brew install python@3.8 pipenv
```
{% endtab %}
{% endtabs %}

#### Set Python Path in \~/.zshrc

{% tabs %}
{% tab title="Bash" %}
```
export PATH="/opt/homebrew/opt/python@3.8/bin:$PATH"
```
{% endtab %}
{% endtabs %}

#### Refresh \~/.zshrc&#x20;

{% tabs %}
{% tab title="Bash" %}
```
source ~/.zshrc
```
{% endtab %}
{% endtabs %}

## Step 3: Install packages

{% tabs %}
{% tab title="Bash" %}
```
PIPENV_VENV_IN_PROJECT=1 
cd ~/myinfo-connector-python
pipenv install 
```
{% endtab %}
{% endtabs %}

## Step 4: Start server

{% tabs %}
{% tab title="First Tab" %}
```
pipenv run ./connector/manage.py runserver 0:3001
```
{% endtab %}

{% tab title="Second Tab" %}

{% endtab %}
{% endtabs %}

##

## Make your request

To make your first request, call apis in order.&#x20;

#### Step 1: Get myinfo redirect login url

{% swagger method="get" path="/users/me/external/myinfo-redirect-login" baseUrl="http://localhost:3001" summary="Get login url to redirect to myinfo" %}
{% swagger-description %}
Get login url to redirect to myinfo. This url contains attributes about person data requested to myinfo.




{% endswagger-description %}

{% swagger-response status="200: OK" description="" %}
```json
{
    "message": "OK",
    "data": {
        "url": "https://test.api.myinfo.gov.sg/com/v3/authorise?client_id=STG2-MYINFO-SELF-TEST&attributes=name,dob,birthcountry,nationality,uinfin,sex,regadd&state=eb03c000-00a3-4708-ab30-926306bfc4a8&redirect_uri=http://localhost:3001/callback&purpose=python-myinfo-connector",
        "state": "eb03c000-00a3-4708-ab30-926306bfc4a8"
    }
}
```



url: url to redirect to myinfo.&#x20;

state: Identifier that represents the user's session/transaction with the client for reconciling query and response. The same value will be sent back via the callback URL. Use a unique system generated number for each user/transaction.


{% endswagger-response %}
{% endswagger %}

####

#### Step 2: Browse myinfo redirect login url&#x20;

{% tabs %}
{% tab title="Curl" %}
```
curl https://test.api.myinfo.gov.sg/com/v3/authorise?client_id=STG2-MYINFO-SELF-TEST&attributes=name,dob,birthcountry,nationality,uinfin,sex,regadd&state=eb03c000-00a3-4708-ab30-926306bfc4a8&redirect_uri=http://localhost:3001/callback&purpose=python-myinfo-connector// Some code
```
{% endtab %}
{% endtabs %}



#### Step 3: Do login and check agree terms

![Myinfo Login Page](<../.gitbook/assets/스크린샷 2022-07-23 오전 10.18.51.png>)



![Myinfo Terms Agreement Page](<../.gitbook/assets/스크린샷 2022-07-23 오전 10.19.35.png>)



#### (Automated) Step 4: Callback API get called by Myinfo&#x20;

After login, Myinfo call our callback api to pass auth code as a response.&#x20;

{% swagger method="get" path="/callback" baseUrl="http://localhost:3001" summary="Callback called by Myinfo service" %}
{% swagger-description %}
After login Myinfo and agree terms, Myinfo service automatically call myinfo-connector-python's callback API to pass auth code.
{% endswagger-description %}

{% swagger-parameter in="query" required="true" name="code" %}
The authcode given by 

[myinfo's authorise API](https://public.cloud.myinfo.gov.sg/myinfo/api/myinfo-kyc-v3.2.2.html#operation/getauthorise)


{% endswagger-parameter %}

{% swagger-parameter in="query" name="state" required="true" %}
Identifier that represents the user's session/transaction with the client for reconciling query and response. The same value will be sent back via the callback URL. Use a unique system generated number for each user/transaction.
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="successfully called by Myinfo" %}
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <span>Callback from Myinfo. Wait for calling person data API</span>
    <span id="code-container">{{ code }}</span>
</body>

<script>
    window.addEventListener('load',
        function (event) {
            let code_element = document.getElementById('code-container')
            let code_text = code_element.innerHTML

            let url = `http://localhost:3001/users/me/external/myinfo?code=${code_text}`
            window.location.href = url
        }
    );
</script>
</html>

```
{% endswagger-response %}
{% endswagger %}

callback url example

```
http://localhost:3001/callback?code=8932a98da8720a10e356bc76475d76c4c628aa7f&state=e2ad339a-337f-45ec-98fa-1672160cf463
```



Our callback api return simple HTML page to&#x20;

* get callback from Myinfo&#x20;
* call api for person data automatically right after the callback api called.&#x20;



![Response Page for callback api](<../.gitbook/assets/스크린샷 2022-07-23 오전 10.32.34.png>)





#### (Automated) Final Step: Get Person data from Myinfo

After callback, callback page automatically calls our api for person data

{% swagger method="get" path="/users/me/external/myinfo" baseUrl="http://localhost:3001" summary="Get person data from myinfo" %}
{% swagger-description %}
Get person data from myinfo. The API is final step of myinfo-connector-python
{% endswagger-description %}

{% swagger-parameter in="query" name="code" %}
The authcode given by 

[myinfo's authorise API](https://public.cloud.myinfo.gov.sg/myinfo/api/myinfo-kyc-v3.2.2.html#operation/getauthorise)


{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Succeed to get person data from myinfo" %}
```json
{
    "message": "OK",
    "sodata": {
        "regadd": {
            "country": {
                "code": "SG",
                "desc": "SINGAPORE"
            },
            "unit": {
                "value": "10"
            },
            "street": {
                "value": "ANCHORVALE DRIVE"
            },
            "lastupdated": "2022-07-14",
            "block": {
                "value": "319"
            },
            "source": "1",
            "postal": {
                "value": "542319"
            },
            "classification": "C",
            "floor": {
                "value": "38"
            },
            "type": "SG",
            "building": {
                "value": ""
            }
        },
        "dob": "1988-10-06",
        "sex": "M",
        "name": "ANDY LAU",
        "birthcountry": "SG",
        "nationality": "SG",
        "uinfin": "S6005048A"
    }
}
```



regadd: Registered Address of Person (including FIN holders)

dob: Date of Birth of Person.

sex: Sex of Person.

name: Full Name of the Person.

birthcountry: Country of Birth of Person. Refer to `country` in code table provided [HERE](https://public.cloud.myinfo.gov.sg/myinfobiz/myinfo-business-api-code-tables.xlsx) for description of each code.

nationality: Nationality of Person. Refer to the [Code reference tables](https://public.cloud.myinfo.gov.sg/myinfo/api/myinfo-kyc-v3.2.2.html#section/Support) in the Support section for list of possible values.

unifin: Singapore issued identification number of the Person.
{% endswagger-response %}

{% swagger-response status="500: Internal Server Error" description="Failed to get person data from myinfo" %}
{% tabs %}
{% tab title="AccessToken failure" %}
```
{
    "message": "Internal server error.",
    "code": "INTERNAL_SERVER_ERROR",
    "error": {
        "title": "오류가 발생했습니다.",
        "description": "Fail to get Myinfo Access Token. Authcode Might be re-used or expired.",
        "code": "INTERNAL_SERVER_ERROR",
        "data": null
    }
}
```
{% endtab %}

{% tab title="Person failure" %}
```
{
    "message": "Internal server error.",
    "code": "INTERNAL_SERVER_ERROR",
    "error": {
        "title": "오류가 발생했습니다.",
        "description": "Fail to parse Myinfo Person Data.",
        "code": "INTERNAL_SERVER_ERROR",
        "data": null
    }
}
```
{% endtab %}
{% endtabs %}
{% endswagger-response %}
{% endswagger %}

