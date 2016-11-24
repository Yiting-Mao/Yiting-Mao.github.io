---
layout: post
title:  "Tsung code to extract a random link using xpath"
date:   2016-11-24 11:20:28 -0700
categories: tsung xpath erlang
---

Tsung is a tool for load testing. Though Tsung has an [official document][tsung-document], it is very concise. There is not much resource regarding to advanced features, so it took me quite a while to figure out how to get a random link from a page. 

### Code snippet: ###

	<request> 
		<dyn_variable name='all_item' xpath="/html/body/div/div[2]/div[*]/div/a/@href"/>
		<http url='/bid_items' version='1.1' method='GET'></http>
	</request>

	<setdynvars sourcetype="eval"
		code='fun({Pid,DynVars})->
		    {ok,Val}=ts_dynvars:lookup(all_item,DynVars),
		    random:seed(now()),
		    lists:nth(random:uniform(length(Val)), Val) end.' >
		<var name="nth_item" />
	</setdynvars>

    <request subst="true">    
        <http url='%%_nth_item%%' version='1.1' method='GET'></http>
    </request>

### Explaination: ###

This snippet mimics a user going to the bid_items index page, then randomly follows one item link to view its detail. 

It first uses xpath to capture all items' links into variable `all_item`. Please note the usage of `div[*]`, which captures all links matching this pattern. 

In the setdynvars section, Tsung evaluates a segment of erlang code, and stores its value into variable `nth_item`. Within the code segment is an anonymous function, which takes in two Tsung arguments. The function returns a random entry in the list  `all_item`. 

[tsung-document]:http://tsung.erlang-projects.org/user_manual/index.html