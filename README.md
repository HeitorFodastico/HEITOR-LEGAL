# HEITOR-LEGAL
// ==UserScript==
// @name       Heitor Monteiro Xavier
// @author     Heitor
// @version    1
// @namespace  aaaa
// @match      https://debianforum.de/forum/viewtopic.php*
// @run-at     yo quiero gozar
// ==/UserScript==

/*
 *
 *	Purpose:	- Hide Posts of specific users in thread view
 *			- can remove signatures of all users if enabled
 *
 */

(() => {
  "use strict";

  const hide_signatures = 1;		// Remove signatures from all posts
  const log_enabled 	= 0;		// Enable logging - for debugging purposes
  var hiddenUsers;

  // put usernames (in doublequotes ("username") to hide here)
  hiddenUsers = [
  ];

  function mylog(text) {
   if (log_enabled) {
   		console.log(text);
   }
 }

 function removeRow(row) {

    /* Since we are removing a row, shift the light and dark grey
     * backgrounds accordingly.
     */

   let prevRow = row;
   let prevClasses = row.className;
   let myRow = row.nextElementSibling;
   while (myRow) {
     const classes = myRow.className;
     myRow.className = prevClasses;
     prevClasses = classes;
     prevRow = myRow;
     myRow = prevRow.nextElementSibling;
   }
	 mylog("  removing row");
   row.remove();
 }

  function hidePostsByUsername(node) {
        const usernameElems = node.querySelectorAll?.("span strong a.username, span strong a.username-coloured") ?? [];
        for (const usernameElem of usernameElems) {
          mylog ("found username: "+usernameElem.textContent);
          if (!hiddenUsers.includes(usernameElem.textContent)) {
            continue;
          }
          mylog("found user thread of user " + usernameElem.textContent + " to hide");
          removeRow(usernameElem.closest("div.inner"));
  	}
   }

  function hideSignatures() {

    if(hide_signatures === 1) {
    	// hide all signatures - if enabled
    	var signatures = document.getElementsByClassName("signature");
    	for (const sig of signatures) {
	    	sig.style.display = "none";
  	  }
    }
  }


  function hideThings(mutations) {
    hideSignatures();
    for (const mutation of mutations) {
      for (const node of mutation.addedNodes) {
        hidePostsByUsername(node);

      }
    }
  }

  const observer = new MutationObserver(hideThings);
  observer.observe(document.documentElement, { childList: true, subtree: true });
  addEventListener("load");
})();
