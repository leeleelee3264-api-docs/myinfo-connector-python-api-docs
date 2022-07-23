# Callback



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
