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