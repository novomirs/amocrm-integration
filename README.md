# amocrm-integration
Примеры кода для работы с API AmoCRM

```
// код ниже включает вывод ошибок
// ini_set('error_reporting', E_ALL);
// ini_set('display_errors', 1);
// ini_set('display_startup_errors', 1);
```

## 1. Авторизация в AMO по API
```
$user = array(
  'USER_LOGIN'=>'..ваш логин..', 
  'USER_HASH'=>'..API KEY..' 
);

$subdomain = '..субдомен..'; 


#авторизация
$link = 'https://'.$subdomain.'.amocrm.ru/private/api/auth.php?type=json';

$curl = set_curl($link, http_build_query($user));
$out = curl_exec($curl); 
$code=curl_getinfo($curl,CURLINFO_HTTP_CODE);

$code=(int)$code;
$errors=array(
    301=>'Moved permanently',
    400=>'Bad request',
    401=>'Unauthorized',
    403=>'Forbidden',
    404=>'Not found',
    500=>'Internal server error',
    502=>'Bad gateway',
    503=>'Service unavailable'
);
try
{
    #Если код ответа не равен 200 или 204 - возвращаем сообщение об ошибке
    if($code!=200 && $code!=204)
    throw new Exception(isset($errors[$code]) ? $errors[$code] : 'Undescribed error',$code);
}
catch(Exception $E)
{
    print_r('Ошибка: '.$E->getMessage().PHP_EOL.'Код ошибки: '.$E->getCode());
    $no_errors=false;
}

curl_close($curl); #Завершаем сеанс cURL

$Response = json_decode($out,true);
$Response = $Response['response'];



function set_curl($link,$data){
    $curl=curl_init();
    curl_setopt($curl,CURLOPT_RETURNTRANSFER,true);
    curl_setopt($curl,CURLOPT_USERAGENT,'amoCRM-API-client/1.0');
    curl_setopt($curl,CURLOPT_URL,$link);
    curl_setopt($curl,CURLOPT_CUSTOMREQUEST,'POST');
    curl_setopt($curl,CURLOPT_POST, true);
    curl_setopt($curl,CURLOPT_POSTFIELDS, $data);
    //curl_setopt($curl,CURLOPT_HTTPHEADER,array('Content-Type: application/json'));
    curl_setopt($curl,CURLOPT_HEADER,false);
    curl_setopt($curl,CURLOPT_COOKIEFILE,dirname(__FILE__).'/cookie.txt'); #PHP>5.3.6 dirname(__FILE__) -> __DIR__
    curl_setopt($curl,CURLOPT_COOKIEJAR,dirname(__FILE__).'/cookie.txt'); #PHP>5.3.6 dirname(__FILE__) -> __DIR__
    curl_setopt($curl,CURLOPT_SSL_VERIFYPEER,0);
    curl_setopt($curl,CURLOPT_SSL_VERIFYHOST,0);
    return $curl;
}

```
