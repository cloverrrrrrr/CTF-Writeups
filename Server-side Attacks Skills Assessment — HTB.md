## **Introduction**

hello everyone in this write up I will walk you through the skill assessment on server-side attacks module in htb.

## Identification

first thing first we need to find an injection point for testing our payload, while analyzing the request in this website I found some interesting requests that points to an api called truckapi.htb that this api used for called truck location and there’s an id of the identification to point, let’s test it.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:770/1*XKcXY8rNw4BOkGVxG_xEfQ.png)


since we found the the endpoint we’ll try test a basic ssti to see the respose and confirmed the vulnerability, I use the basic payload {{77}} and as we can see the response returned is 49 which means the input is vulnerable to ssti is either use the Jinja2 or Twig but since the wappalyzer confirmed it uses Apache we can confirm it use Twig.

![](https://miro.medium.com/v2/resize:fit:554/1*eAN81s4dNQuQoekxHcKKxQ.png)

After we can confirmed that they used Twig we can just test for the RCE to see the response before we can find the flag I used the payload from the PayloadAllTheThings and tested the command here I use the id and as expected it reflected the id of the systems.

![](https://miro.medium.com/v2/resize:fit:551/1*nQ5LOkiBOB0nddEXIHUFGg.png)

Now we can confirm the vulnerability and we can use it to retrieve the flag

![](https://miro.medium.com/v2/resize:fit:551/1*6YaROk9aojc0ej0DV8OIUA.png)
