nginx-ustats-module
============

#### module provides basic statistics for each backend in nginx upstreams:

* Number of requests
* Http 499/500/503 errors count
* Tcp errors
* Http read/write timeouts
* Fail timeout
* Max fails count
* Last failed access time
* Total fails count
* Blacklisted backend highlighting
* Down backends highlighting

The module's **web interface** provides a good visual representation of what's going on with your backends. Values in some columns of the table can be sorted within each upstream row.

Gathered data can also be retrieved in **JSON format**. To do so, append `?json=1` to the end of location on which the module was set to work on (see configuration instructions below).

* Json Show:
request url:    
`http://127.0.0.1:8045/monitor?json=1 `   

```json    
{
	"bar":
	[
		["127.0.0.1:80", 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 10, 1, "0000-00-00 00:00:00", "0000-00-00 00:00:00", 0],
		["127.0.0.1:81", 0, 0, 1320, 3, 44, 0, 0, 0, 0, 1320, 0, 0, 1, 10, 1, "2016-01-21 15:18:19", "2016-01-21 15:19:19", 1853],
		["61.135.169.125:80 (www.baidu.com)", 0, 0, 1476, 1, 160, 0, 0, 0, 0, 0, 2, 0, 1, 10, 1, "2016-01-21 15:18:19", "2016-01-21 15:19:19", 1942],
		["61.135.169.121:80 (www.baidu.com)", 0, 0, 1498, 1, 18, 0, 0, 0, 0, 0, 0, 0, 1, 10, 1, "2016-01-21 15:18:19", "2016-01-21 15:18:48", 1947],
		0
	],
	"www.baidu.com":
	[
		["61.135.169.121:80 (www.baidu.com)", 0, 0, 687, 48, 861, 0, 0, 0, 0, 0, 0, 0, 1, 10, 1, "2016-01-21 15:19:38", "2016-01-21 15:19:46", 338],
		["61.135.169.125:80 (www.baidu.com)", 0, 0, 683, 40, 793, 0, 0, 0, 0, 0, 0, 0, 1, 10, 1, "2016-01-21 15:19:38", "2016-01-21 15:19:47", 353],
		1
	]
}

```       
upstream name:     
[   
    [peer_name, down, failed, requests, RPS, SPEED, 499, 5xx, 500, 503, tcp_errors, R_timeouts, w_timeouts, weight, f_t, m_f, s_t, last_fail_t, tot_f],
implicit    
]

`implicit`: means different upstream (if implicit 1 means 'implicit' upstream for example `proxy_pass`). 

5xx:  499 < x < 600 explict 500 503
* Html Show    
request url:   
`http://127.0.0.1:8045/monitor?json=1 `  

![Screenshot](./doc/monitor.jpg)

* Note    
 `this module not support `dynamic manage upstream` forexample 'tengine dyups module'.`

#### TODO
* support `dyups` module.


#### Installation

* tested with tengine-2.1.1
Copy `nginx-1.6.2.patch` file into nginx root folder
cd into nginx root folder and apply the patch:

    patch -p1 -i nginx-1.6.2.patch
    
Run ./configure with all parameters you normally use, appending option
    
    --add-module=src/http/modules/ngx_ustats_module
    make && make install


#### Configuration

*Example*

    location /ustats {
        ustats memsize=3m;
        ustats_refresh_interval 6000;
        ustats_html_table_width 95;
        ustats_html_table_height 95;
    }

The module supports a number of configuration directives:

* syntax: **ustats memsize=\<size\>;**

  context: location

  Enables module handler for this location and sets the size of the shared memory that will be used to store statistics data across all worker processes.

  Example: `ustats memsize=2m;`


* syntax: **ustats_html_table_width \<number\>;**
  
  context: location

  default: 70

  Specifies web interface table width. Values less or equal to 100 are interpreted as percents, otherwise as pixels.


* syntax: **ustats_html_table_height \<number\>;**

  context: location

  default: 70

  See _ustats_html_table_width_.


* syntax: **ustats_refresh_interval \<number\>;**

  context: location

  default: 5000

  Specifies page refresh interval in milliseconds.
