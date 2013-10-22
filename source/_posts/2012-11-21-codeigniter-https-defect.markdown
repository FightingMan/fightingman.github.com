---
layout: post
title: "Codeigniter https defect"
date: 2012-11-21 17:05
comments: true
categories: PHP
---
Today I'm test a small experiment with CI and nginx,I found that:if I use its form helper submit the data ,http protocol will jump to https protocol,then I read the source code found that:
    {% codeblock Config.php %}
        ...
		if ($this->config['base_url'] == '')
		{
			if (isset($_SERVER['HTTP_HOST']))
			{
				$base_url = isset($_SERVER['HTTPS']) && strtolower($_SERVER['HTTPS']) !== 'off' ? 'https' : 'http';
				$base_url .= '://'. $_SERVER['HTTP_HOST'];
				$base_url .= str_replace(basename($_SERVER['SCRIPT_NAME']), '', $_SERVER['SCRIPT_NAME']);
			}

			else
			{
				$base_url = 'http://localhost/';
			}

			$this->set_item('base_url', $base_url);
		}
        ...
    {% endcodeblock %}
Which means if you forget to set the value of base_url in config.php, CI will set it automatic,and if you use nginx and install the ssl module,the $_SERVER['HTTPS'] has been setted,it will be an empty string if you don't use it.
TO solve it, you must set value of base_url in config.php
    {% codeblock config.php %}
        $_SERVER['HTTPS'] = 'off';// if u don't use https,or set the base_url $config['base_url']   = 'http://yoursite';
    {% endcodeblock %}

