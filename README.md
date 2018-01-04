# parse-everything
notes about parsers

## 1. Large XML file (> 1 GB)

XMLReader + SimpleXMLElement

```php
ini_set('memory_limit','1536M'); // 1.5 GB
ini_set('max_execution_time', 18000); // 5 hours

$filename = plugin_dir_url( __FILE__ ).'data.xml';

$reader = new XMLReader;
$r = $reader->open($filename);

while( $reader->read() ) {
    switch ($reader->nodeType) {
        case (XMLREADER::ELEMENT):
            if ($reader->localName == 'some_node_name') {
                $node_xml = $reader->readOuterXML();
                $node = new SimpleXMLElement($node_xml);
                
                $some_data = (string)$node['SomeData'];
                //...
    }
}

$reader->close();
     
```

## 2. CSV file

```php

$filename = plugin_dir_url( __FILE__ ).'data.csv';
$data = explode("\n", file_get_contents($filename));
foreach ($data as $entry) {
    $item = str_getcsv($entry, ";", "\""); 
    $some_data_0 = $item[0];
    $some_data_1 = $item[1];
    //...		
}
```		

## 3. Website

Simple HTML DOM
http://simplehtmldom.sourceforge.net/manual.htm

```php	
$curl = curl_init(); 
curl_setopt($curl, CURLOPT_URL, $url);  
curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);  
curl_setopt($curl, CURLOPT_CONNECTTIMEOUT, 10);  
curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, false);
$str = curl_exec($curl);  
curl_close($curl);  
$page = str_get_html($str); 

$div_selector1 = $page->find('div#selector1',0);
if(!empty($div_selector1)) {
    $links = $div_selector1->find('p>a');
    $td_selector2 = $div_selector1->find('td.selector2',1);
}

//if info blocks has no css
$text_stack = array();

//skipped !empty check for $td_selector2
$texts = $td_selector2->find('text');
foreach ($texts as $text) {
    $t = trim(($text->plaintext));
    if (!empty($t)) $text_stack[] = $t; 
}

```	

```php	
$data = array();

$element = array('title'=>reset($text_stack));

while ($t = next($text_stack)) {
    if (strstr($t,'Адрес')) {
        $element['adres'] = next($text_stack);
    }
    else if (strstr($t,'Телефон')) {
        $element['telefon'] = next($text_stack);
    }
    else if (strstr($t,'Email')) {
        $element['email'] = next($text_stack);
    }
    else if (strstr($t,'Факс')) {
        $element['fax'] = next($text_stack);
    }
    else if (strstr($t,'Сайт')) {
        $element['site'] = next($text_stack);
    }
    else if (strstr($t,'Ссылки на социальные сети')) {
        $element['social'] = next($text_stack);
    }
    else {
        if (array_key_exists ( 'adres', $element )) {
            //если адрес уже заполнен и встретился произвольный текст - значит, это новый элемент
            //если нет - это следующая строка заголовка 
            $element['title'] = trim($element['title']);
            $data[] = $element;

            $element = array('title'=>'');
        }
        $element['title'] .= ' '.$t;
    }
}
$element['title'] = trim($element['title']);
$data[] = $element;
```	
