#  Fetch data from PWA Project Online

```php

<?php

set_time_limit(0);

require_once 'SPOClient.php';

function connectSPO($url,$username,$password)
{
    try {
        $client = new SPOClient($url);
        $client->signIn($username,$password);
		return $client;
        echo 'You have been authenticated successfully\n';
    }
    catch (Exception $e) {
        echo 'Authentication failed: ',  $e->getMessage(), "\n";
    }
}

$username = 'USERNAME-OFFICE-365';
$password = 'PASSWORD-OFFICE-365';

$i=0;  										// register counter

$limit = 300;  								// limite counter from Project Online (projects)

$skipper = 0;  								// skipper counter
$topper  = $limit;							// topper counter
$total_registro = 0;

$ROUTINE = "Projetos";

$start = date('Y-m-d G:i:s');

do {
		// Sharepoint PWA Server API
        $url = 'https://YOUR-SHAREPOINT-DOMAIN.sharepoint.com/sites/pwa/_api/projectdata/$ROUTINE';

        $client = connectSPO($url,$username,$password);

        $options = array(
                      'list' => '?$format=json&$skip='.$skipper.'&$top='.$topper,
                      'id' => NULL,
                      'method' => 'GET'
                   );

        $content = $client->getHeaders($options);

        $total_registro += sizeof($content->value);

		for ($i=0;$i<sizeof($content->value);$i++) {

			$data = object_2_array($content->value[$i]);
			
			// Print data from server
			var_dump($data);

		}

 if (sizeof($content->value) == 0) break;

    $skipper += $limit;
    ++$i;

} while (1);

$end = date('Y-m-d G:i:s');

echo "Starts at $start time";
echo "Ends at $end time";

function object_2_array($result)
{
	$array = array();

	foreach ($result as $key=>$value)
	{
		$newKey = toASCII(utf8_decode($key));

		if (is_object($value))
		{
			$array[$newKey]=object_2_array($value);
		}
		elseif (is_array($value))
		{
			$array[$newKey]=object_2_array($value);
		}
		else
		{
			$array[$newKey]=$value;
		}
	}
	return $array;
}

?>

```
