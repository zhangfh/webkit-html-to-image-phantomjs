webkit-html-to-image-phantomjs
==============================

A simple Phantom.js webkit HTML to Image conversion service

Installation
============

The following installation steps have been verified to work on Ubuntu:

1. **Download from Github:**

    ```bash
    git clone https://github.com/nathanpeck/webkit-html-to-image-phantomjs.git
    ```   

2. **Install node.js:**

    ```bash
    sudo apt-get install python-software-properties python g++ make
    sudo add-apt-repository ppa:chris-lea/node.js
    sudo apt-get update
    sudo apt-get install nodejs npm
    I cannot update repository success, so firstly, I using sudo apt-get install nodejs to install nodejs.
    How to install npm:
      curl -O -L -k https://npmjs.org/install.sh
      sudo sh install.sh
    ```

3. **Install phantom.js:** (Instructions assume a 64 bit system. Substitute the 32 bit version if needed.)

    ```bash
    wget https://phantomjs.googlecode.com/files/phantomjs-1.9.0-linux-i686.tar.bz2
    tar xvf phantomjs-1.9.0-linux-i686.tar.bz2
    sudo mv phantomjs-1.9.0-linux-i686/bin/phantomjs /usr/bin/phantomjs
    ```

4. **Install needed dependencies:**

    ```bash
    sudo npm install -g supervisor
    cd webkit-html-to-image-phantomjs
    npm install
    ```
    
5. **Start the server:**

    ```bash
    ./launch.sh
    ```
6. **Test:**

    ```bash
    I can run this server succesfully
    Using command line to test:
    #curl -X POST -H "Content-Type: text/html" -d     '{"cities":["Seoul","Shanghai","Taipei"]}' 192.168.1.8:3000
    It will output result to the console, I change the code as following:
    imageData = page.renderBase64('PNG');==>imageData = page.render('./test.png'); (save it)
    imageData = page.render('./test.png');==>page.viewportSize = { width: 384, height: 554 };(change size)
    Using php to test it(I write a sample code to test it, assume I have a static webpage，I will capture it)
    <?php

    function httpGet() {
    $url = 'http://127.0.0.1:8888/template.php';
    
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, $url);
    curl_setopt($ch, CURLOPT_HEADER, TRUE);    //表示需要response header
    curl_setopt($ch, CURLOPT_NOBODY, FALSE); //表示需要response body
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, TRUE);
    curl_setopt($ch, CURLOPT_FOLLOWLOCATION, FALSE);
    curl_setopt($ch, CURLOPT_AUTOREFERER, TRUE);
    curl_setopt($ch, CURLOPT_TIMEOUT, 120);
    
    $result = curl_exec($ch);
    /*
    if (curl_getinfo($ch, CURLINFO_HTTP_CODE) == '200') {
        return $result;
    }
    */
    if (curl_getinfo($ch, CURLINFO_HTTP_CODE) == '200') {
      $headerSize = curl_getinfo($ch, CURLINFO_HEADER_SIZE);
      $header = substr($result, 0, $headerSize);
      $body = substr($result, $headerSize);
    //echo $body;
      return $body;
    }   
    return NULL;
    }
    echo '1';
    
    echo '<br />';
  function FormatHeader($url)  
  {  
  // 解悉url  
    $temp = parse_url($url);  
    $query = isset($temp['query']) ? $temp['query'] : '';  
    $path = isset($temp['path']) ? $temp['path'] : '/';  
  
    $header = array (  
    "POST {$path}?{$query} HTTP/1.1",  
    "Host: {$temp['host']}",  
    "Content-Type: text/html",  
    'Accept: */*',  
    "Referer: http://{$temp['host']}/",  
    'User-Agent: Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; SV1)',  
    //"Content-length: 380",  
    "Connection: Close"  
    );  
  
 return $header;  
} 


	$body = httpGet();
    $interface = '192.168.1.103:3000';  //phantomjs server
    $header = FormatHeader($interface); 
     $url = "192.168.1.103:3000";
     $ch = curl_init();
     curl_setopt($ch, CURLOPT_HTTPHEADER, $header);
     curl_setopt($ch, CURLOPT_URL, $url);
     curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
     curl_setopt($ch, CURLOPT_POST, 1);
     curl_setopt($ch, CURLOPT_POSTFIELDS, $body);
     
     curl_exec($ch);
     curl_close($ch);
?>
    ```
