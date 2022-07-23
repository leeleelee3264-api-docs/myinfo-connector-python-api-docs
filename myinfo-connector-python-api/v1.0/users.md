# Users

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
