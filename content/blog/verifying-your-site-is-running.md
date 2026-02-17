---
title: 'Verifying your site is running?'
date: Tue, 02 Mar 2004 08:00:00 +0000
draft: false
tags: ['DevOps', 'Web Development']
---

This question often comes up-how do you make sure that your site is running? One solution is to run tools like [WhatsUp Gold](http://www.ipswitch.com/products/whatsup/index.asp) that monitors your IIS service status, SQL service status etc. As long as these services respond as saying they are running, WhatsUp gold is happy. But unfortunately, the fact that these services are running does not mean that your website is functioning to the public. For example if you have a page that depends on an external web service, and for some reason the external web service is down, then your page will not respond and will eventually time out. To the public your site is down. WhatsUp gold is not necessarily going to check for that. I say not necessarily because it is possible to have WhatsUp gold monitor HTTP status, but that creates a strong coupling between WhatsUp gold and generic application development.

```perl
 error_reporting(E_ERROR);

class servers {
	public $settings = array();
	const maxErrors = 5;
		        
	public function add($host, $port, $url, $maxLines, $search, $subject, $to, $cc, $bcc) {
		array_push($this->settings, 
			array(
				"host"		=> $host,
				"port"		=> $port,
				"url" 		=> $url,
				"search" 	=> $search,
				"maxLines"	=> $maxLines,
				"subject"	=> $subject,
				"to" 		=> $to,
				"cc" 		=> $cc,
				"bcc" 		=> $bcc
			)
		);
	}
}
function lastState($server, $reset=false) {
	$errorCount = 0;
	$filename = md5($server["host"]);
	$exists = file_exists($filename);
	
	if(!$reset) {
		if($exists) $status = fopen($filename,"rw+");
		else $status = fopen($filename, "x+");
	
		if(!$status) {
			echo("Failed to open $filename (status file)\r\n");
		}
	
		$errorCount = (fread($status, 1)+0);
		$newCount = (($errorCount+1)."");
		rewind($status);

		fwrite($status, $newCount, strlen($newCount));
		fclose($status);
	}
	else {
		if($exists) unlink($filename);
	}
	return $errorCount;
}
function error($msg, $server, $sendmail=true) {
	echo "FAILED,\"", str_replace("\"", "'", $msg), "\"";
	if($sendmail) {
		$errorCount = lastState($server);

		if($errorCount < servers::maxErrors && $errorCount>0) {
			$headers = "From: \"System Administrators\" <systems@law.ucla.edu>\r\nPriority: Urgent\r\nImportance: high";
			if($server["cc"] != "") {
				$headers .= "\r\nCC: {$server["cc"]}";
			}
			if($server["bcc"] != "") {
				$headers .= "\r\nBCC: {$server["bcc"]}";
			}
			mail($server["to"], $server["subject"], $msg, $headers);
		}
		else {
			echo ",\"(error count is $errorCount)\"";
		}
	}
} 
```

```perl
 require("functions.inc.php");

$verifyServers = new servers();
$verifyServers->add("www.law.ucla.edu", 		80,	"/home/Default.aspx",	40,	"UCLA School of Law", 				"UCLAW Down",		"systems@law.ucla.edu", "salman@law.ucla.edu",	"myvirustech@vtext.com,8186481722@vtext.com,3105023584@vtext.com");
$verifyServers->add("eres.lawlib.ucla.edu",		80,	"/eres/Default.aspx",	80,	"Docutek ERes - Ereserves Home",		"ERes Down",		"systems@law.ucla.edu", "salman@law.ucla.edu",	"myvirustech@vtext.com,3105023584@vtext.com");
//$verifyServers->add("ssl://lawnet.ucla.edu",		443,	"/index.html",		120,	"UCLA School of Law Student Email System",	"IMail Down",		"systems@law.ucla.edu",	"salman@law.ucla.edu", 	"myvirustech@vtext.com,3105023584@vtext.com");
$verifyServers->add("cyberpay.law.ucla.edu",		80,	"/Default.aspx",	40,	"CyberPay | UCLA Law",				"CyberPay Down",	"systems@law.ucla.edu",	"salman@law.ucla.edu",	"myvirustech@vtext.com,8186481722@vtext.com,3105023584@vtext.com");
$verifyServers->add("weblog.law.ucla.edu",		80,	"/index.html",		40,	"UCLA School of Law Blogs",			"Blog Down",		"systems@law.ucla.edu",	"salman@law.ucla.edu",	"");
//$verifyServers->add("ssl://webapp.law.ucla.edu",	443,	"/Default.asp",		40,	"UCLA School of Law - Online Application",	"J.D. Down",		"systems@law.ucla.edu",	"salman@law.ucla.edu",	"");
$verifyServers->add("www.uclalawreview.org",		80,	"/index.aspx",		30,	"Current Issue | UCLA Law Review",		"Law Review Down",	"systems@law.ucla.edu",	"salman@law.ucla.edu",	"");

foreach($verifyServers->settings as $server) {
	try {
		echo date("Y-m-d H:i:s"), ",", $server["host"], ",";
		$file=fsockopen($server["host"],$server["port"],$errno,$errstr,30);
		if(!$file) {
			if(strlen($errstr) == 0) $errstr="Unexpected error";
			error("$errstr ($errno)", $server);
		}
		else {
			$httpRequest = "GET {$server["url"]} HTTP/1.1\r\n";
			$httpRequest .= "Host: {$server["host"]}\r\n";
			$httpRequest .= "User-Agent: Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; .NET CLR 1.1.4322; .NET CLR 2.0.50727)\r\n";
			$httpRequest .= "Connection: Close\r\n\r\n";
		
			fwrite($file, $httpRequest);
			$found = false;
			$lines = 0;
			while(!feof($file)) {
				if(++$lines < $server["maxLines"] && strpos(fgets($file), $server["search"]) !== FALSE) {
					$found = true;
					break;
				}
				else if($lines > $server["maxLines"]) break;
				
			}
			if(!$found) {
				error("content error", $server);
			}
			else {
				echo "OK";
				lastState($server, true);
			}
		}
		fclose($file);
		echo "\r\n";
	}
	catch(Exception $ex) {
		error($ex->Message, $server);
	}
} 
```