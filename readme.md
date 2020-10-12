## About this script

This script is intended to make life easier in order to follow people in Twitter.
Twitter limits the follows to around 400 per day.
Therefore, the motivation is being able to go to the list of followers of any entity in Twitter,
paste this script in the console, and let it do the work.

## Instructions

1. Go to any page in Twitter with "Follow" buttons.
2. Open the browser inspector
3. Go to "Console"
4. Paste the script below

## The script

The script you need to paste (minified):

```
const maxScrollDownAttempt=3,delay=3,delayRandom=5,maxFollowed=400;var alreadyFollowed=[];function findDomElementsByText(o,l){return Array.prototype.filter.call(document.querySelectorAll(o),function(o){return l.trim()==o.textContent.trim()})}function getElementName(o){return o.parentElement.parentElement.parentElement.parentElement.previousSibling.textContent}function getFollowButtons(){return findDomElementsByText("div>span>span","Follow")}function scrollDown(){window.scrollTo(0,document.body.scrollHeight)}function clickFollow(o){o.scrollIntoView(),o.click()}function followChain(o=null,l=0){alreadyFollowed.length!=maxFollowed?l!=maxScrollDownAttempt?(null!=o&&0!=o.length||(console.log("No buttons, finding new ones."),o=getFollowButtons()),0==o.length?(console.log("Couldn't find any new button. Scrolling down"),scrollDown(),setTimeout(function(){followChain(null,l+1)},1e3*(delay+Math.random()*delayRandom))):(buttonToClick=o[0],entityName=getElementName(buttonToClick),console.log("Following "+entityName),alreadyFollowed.includes(entityName)?(console.log("The element was supossed to be already followed. Twitter may be blocking."),l+=1):l=0,clickFollow(buttonToClick),buttonsRemaining=o.slice(1),setTimeout(function(){followChain(buttonsRemaining,0)},1e3*(delay+Math.random()*delayRandom)))):console.log("Finished due to reached attempt "+l):console.log("Already followed max amount "+maxFollowed)}followChain();
```

The full script:

```
const maxScrollDownAttempt = 3;
const delay = 3;
const delayRandom = 5;
const maxFollowed = 400;
var alreadyFollowed = [];

function findDomElementsByText(selector, text) {
    return Array.prototype.filter.call(document.querySelectorAll(selector), function(selector) {
        return text.trim() == selector.textContent.trim()
    })
}

function getElementName(el){
    return el.parentElement.parentElement.parentElement.parentElement.previousSibling.textContent;
}

function getFollowButtons(){
    return findDomElementsByText("div>span>span","Follow");
}

function scrollDown(){
    window.scrollTo(0, document.body.scrollHeight)
}

function clickFollow(el){
    el.scrollIntoView();
    el.click();
}

function followChain(buttons=null, attempt=0){
    if(alreadyFollowed.length==maxFollowed){
        console.log("Already followed max amount " + maxFollowed);
        return;
    };
    if(attempt==maxScrollDownAttempt){
        console.log("Finished due to reached attempt " + attempt);
        return;
    }
    if(buttons==null || buttons.length==0){
        console.log("No buttons, finding new ones.")
        buttons = getFollowButtons();
    }
    if(buttons.length==0){
        console.log("Couldn't find any new button. Scrolling down")
        scrollDown();
        setTimeout(function(){
            followChain(null, attempt+1)
        }, (delay + Math.random() * delayRandom) * 1000);
    } else {
        buttonToClick = buttons[0];
        entityName = getElementName(buttonToClick);
        console.log("Following " + entityName);
        if(alreadyFollowed.includes(entityName)){
            console.log("The element was supossed to be already followed. Twitter may be blocking.")
            attempt += 1;
        } else {
            attempt = 0;
        }
        clickFollow(buttonToClick);
        buttonsRemaining = buttons.slice(1)
        setTimeout(function(){
            followChain(buttonsRemaining, 0)
        }, (delay + Math.random() * delayRandom) * 1000);
    }
}

followChain()
```

Cheers