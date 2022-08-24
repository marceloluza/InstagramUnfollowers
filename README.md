# Instagram Unfollowers

[![Maintenance](https://img.shields.io/maintenance/yes/2022)](https://github.com/davidarroyo1234/InstagramUnfollowers)

A nifty tool to see who which users do not follow you back on Instagram.  
<u>Browser-based and requires no downloads or installations!</u>

## **WARNING**

This version utilizes the Instagram API for better performance.  
If you prefer to use the older version please use this [commit](https://github.com/davidarroyo1234/InstagramUnfollowers/tree/50a0bcbc9fe349b8664a74c0e4477bc974d0352b).

## Usage

### Steps:

1.  Copy the following code

```js
"use strict";const INSTAGRAM_HOSTNAME="www.instagram.com";let nonFollowersList=[],userIdsToUnfollow=[],shouldPromptBeforeUnload=!1;function sleep(o){return new Promise(e=>{setTimeout(e,o)})}function getCookie(e){const o="; "+document.cookie,n=o.split(`; ${e}=`);if(2===n.length)return n.pop().split(";").shift()}function afterUrlGenerator(e){return`https://www.instagram.com/graphql/query/?query_hash=3dec7e2c57367ef3da3d987d89f9dbc8&variables={"id":"${getCookie("ds_user_id")}","include_reel":"true","fetch_mutual":"false","first":"24","after":"${e}"}`}function unfollowUserUrlGenerator(e){return`https://www.instagram.com/web/friendships/${e}/unfollow/`}function getElementByClass(e){var o=document.querySelector(e);if(null===o)throw new Error("Unable to find element by class. className: "+e);return o}function getNonFollowerById(o){var e=nonFollowersList.find(e=>e.id.toString()===o.toString());return void 0===e&&console.error("Unable to find user by id. userId: "+o),e}function onToggleUser(){getElementByClass(".iu_selected-count").innerHTML=`[${userIdsToUnfollow.length}]`}function renderResults(e){const o=[...e].sort((e,o)=>e.username>o.username?1:-1),n=(getElementByClass(".iu_toggle-all-checkbox").disabled=!1,getElementByClass(".iu_results-container"));let t=n.innerHTML="";o.forEach(e=>{var o=e.username.substring(0,1).toUpperCase();t!==o&&(t=o,n.innerHTML+=`<div style='margin:1rem;padding:1rem;font-size:2em;border-bottom: 1px solid #333;'>${t}</div>`),n.innerHTML+=`<label style='display:flex;align-items:center;padding:1rem;border-radius:3px;cursor:pointer;'>            <div style='display:flex;align-items:center;flex:1;'>                <img src=${e.profile_pic_url} width='75px' style='border-radius:50%;' />&nbsp;&nbsp;&nbsp;&nbsp;                <div style='display:flex;flex-direction:column;'>                    <span style='font-size:1.75em;'>${e.username}</span>                    <span style='font-size:1em;'>${e.full_name}</span>                </div>                ${e.is_verified?"&nbsp;&nbsp;<div style='background-color:#49adf4;border-radius:50%;padding:0.2rem 0.3rem;font-size:0.35em;height:fit-content;'>✔</div>":""}            </div>            <input class='account-checkbox' type='checkbox' style='height:1.1rem;width:1.1rem;' onchange='toggleUser(${e.id})' />        </label>`}),getElementByClass(".iu_main-btn").remove()}async function run(e){renderResults(nonFollowersList=await getNonFollowersList(e))}function renderOverlay(){let e=!0;document.documentElement.style.backgroundColor="#222";const o=document.createElement("div"),n=(o.setAttribute("class","iu_overlay"),o.setAttribute("style",["background-color:#222","color:#fff","height:100%","font-family:system-ui"].join(";")),o.innerHTML="<header style='position:fixed;top:0;left:0;right:0;display:flex;align-items:center;justify-content:space-between;padding:1rem;height:2.5rem;background-color:#333;z-index:1;'>        <div style='font-family:monospace;font-size:1.5em;cursor:pointer;' onclick='location.reload()'>InstagramUnfollowers</div>        <label style='display:flex;cursor:pointer;'><input type='checkbox' class='iu_include-verified-checkbox' />&nbsp;Include verified</label>        <div>Non-followers: <span class='iu_nonfollower-count' /></div>        <div style='font-size:1.2em;text-decoration:underline;color:red;cursor:pointer;' onclick='unfollow()'>Unfollow Selected <span class='iu_selected-count'>[0]</span></div>        <input type='checkbox' class='iu_toggle-all-checkbox' style='height:1.1rem;width:1.1rem;' onclick='toggleAllUsers(this.checked)' disabled />    </header>    <button class='iu_main-btn' style='position:absolute;left:50%;top:50%;transform:translate(-50%,-50%);font-size:2em;cursor:pointer;height:160px;width:160px;border-radius:50%;background:transparent;color:currentColor;border:1px solid currentColor;'>RUN</button>    <div class='iu_results-container' style='transform:translateY(75px)'></div>",document.body.replaceChildren(o),getElementByClass(".iu_main-btn").addEventListener("click",()=>run(e)),getElementByClass(".iu_include-verified-checkbox"));n.checked=e,n.addEventListener("change",()=>e=!e)}async function getNonFollowersList(o=!0){let n=[],t=!0,r=0,l=0,s=-1;shouldPromptBeforeUnload=!0;let i=`https://www.instagram.com/graphql/query/?query_hash=3dec7e2c57367ef3da3d987d89f9dbc8&variables={"id":"${getCookie("ds_user_id")}","include_reel":"true","fetch_mutual":"false","first":"24"}`;const a=getElementByClass(".iu_main-btn"),d=getElementByClass(".iu_results-container"),c=getElementByClass(".iu_nonfollower-count");for(;t;){let e;try{e=await fetch(i).then(e=>e.json())}catch(e){continue}-1===s&&(s=e.data.user.edge_follow.count),t=e.data.user.edge_follow.page_info.has_next_page,i=afterUrlGenerator(e.data.user.edge_follow.page_info.end_cursor),l+=e.data.user.edge_follow.edges.length,e.data.user.edge_follow.edges.forEach(e=>{!o&&e.node.is_verified||e.node.follows_viewer||n.push(e.node)}),a.innerHTML=Math.round(l/s*100)+"%",d.innerHTML="",c.innerHTML=n.length.toString(),await sleep(Math.floor(400*Math.random())+1e3),6<++r&&(r=0,d.innerHTML='<span style="margin:1rem;">Sleeping 10 secs to prevent getting temp blocked...</span>',await sleep(1e4))}return shouldPromptBeforeUnload=!1,n}function init(){location.hostname!==INSTAGRAM_HOSTNAME?alert("Can be used only on Instagram routes"):(document.title="InstagramUnfollowers",renderOverlay())}window.addEventListener("beforeunload",e=>{if(shouldPromptBeforeUnload)return(e=e||window.event)&&(e.returnValue="Changes you made may not be saved."),"Changes you made may not be saved."}),window.toggleUser=o=>{userIdsToUnfollow=-1===userIdsToUnfollow.indexOf(o)?[...userIdsToUnfollow,o]:userIdsToUnfollow.filter(e=>e!==o),onToggleUser()},window.toggleAllUsers=(o=!1)=>{document.querySelectorAll("input.account-checkbox").forEach(e=>e.checked=o),userIdsToUnfollow=o?[...nonFollowersList]:[],onToggleUser()},window.unfollow=async()=>{if(confirm("Are you sure?")){var e=getCookie("csrftoken");if(void 0===e)throw new Error("csrftoken cookie is undefined");getElementByClass(".iu_toggle-all-checkbox").disabled=!0;const t=getElementByClass(".iu_results-container");t.innerHTML="",shouldPromptBeforeUnload=!0;let o=0;for(const r of userIdsToUnfollow){var n=getNonFollowerById(r);try{await fetch(unfollowUserUrlGenerator(r),{headers:{"content-type":"application/x-www-form-urlencoded","x-csrftoken":e},method:"POST",mode:"cors",credentials:"include"}),t.innerHTML+=`<div style='padding:1rem;'>Unfollowed                <a style='color:inherit' target='_blank' href='${INSTAGRAM_HOSTNAME}/${n.username}/'> ${n.username}</a>                <span style='color:#00ffff'> [${o+1}/${userIdsToUnfollow.length}]</span>            </div>`}catch(e){console.error(e),t.innerHTML+=`<div style='padding:1rem;color:red;'>Failed to unfollow ${n.username} [${o+1}/${userIdsToUnfollow.length}]</div>`}window.scrollTo(0,t.scrollHeight),await sleep(Math.floor(2e3*Math.random())+4e3),(o+=1)%5==0&&(t.innerHTML+='<hr /><div style="padding:1rem;font-size:1.25em;color:#d7d356;">Sleeping 5 minutes to prevent getting temp blocked...</div><hr />',await sleep(3e5))}shouldPromptBeforeUnload=!1,t.innerHTML+="<hr /><div style='padding:1rem;font-size:1.25em;color:#56d756;'>All DONE!</div><hr />"}},init();
```
2. Log in into your account and open the developer console or (Ctrl+Shift+J(Windows) || ⌘+⌥+I (Mac os)) and paste the code.

3.  You will be met with the following interface:

    <img src="./assets/main.png" alt="main" width="100%" />

4.  Click "RUN" to start scanning for users who do not follow you back.
5.  Once it finishes printing the users, you will be met with the following screen which will show you the results:

    <img src="./assets/results.png" alt="main" width="100%" />

6.  If you wish to un-follow any of these users, you can select 1 or more of them via the checkbox next to each user.

## Notes

**_The more users you have to check, more time it will take_**

**_This script has been tested only on Chromium-based browsers_**

## DEV

When introducing new changes to `main.js`, make sure to run the "build" command in-order to automatically format, compress, and convert your code.

## Legal

**Disclaimer:** This is not affiliated, associated, authorized, endorsed by, or in any way officially connected with Instagram.

Use it at your own risk!.
