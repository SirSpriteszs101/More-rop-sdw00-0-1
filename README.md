// ==UserScript==
// @name         StarBreak: Script Controls Menu
// @namespace    https://gist.github.com/coolguy027/4751b097e1ef9ea178a583f235877c0e
// @version      0.1
// @description  Adds a script controls menu to the game
// @author       coolguy027
// @match        http*://*.starbreak.com/*
// @grant        GM_setValue
// @grant        GM_getValue
// @grant        GM_registerMenuCommand
// @downloadURL  https://gist.github.com/coolguy027/4751b097e1ef9ea178a583f235877c0e/raw/starbreak-script-controls-menu.user.js
// @updateURL    https://gist.github.com/coolguy027/4751b097e1ef9ea178a583f235877c0e/raw/starbreak-script-controls-menu.user.js
// ==/UserScript==
/* jshint -W097 */
'use strict';

function check (name, dfltT) {
    if (typeof GM_getValue(name + 'K') === 'undefined'){
        GM_setValue(name + 'K', dfltT);
    }
}
check('menu', 'T');
check('acc', 'L');
check('hide', 'Slash');
check('hil', 'K');
check('zIn', 'Equal');
check('zOut', 'Minus');
//quick swap row 1 (enabled):
check('q1', 'F');//slot 1
check('q2', 'R');//slot 2
check('q3', 'V');//slot 3
//row 2 (disabled):
//check('q4', 'Numpad4');//slot 4
//check('q5', 'Numpad5');//slot 5
//check('q6', 'Numpad6');//slot 6
//row 3 (disabled):
//check('q7', 'Numpad1');//slot 7
//check('q8', 'Numpad2');//slot 8
//check('q9', 'Numpad3');//slot 9

function createElem(tag, parent) {
    let e = document.createElement(tag);
    e.className = 'mnu';
    if (typeof parent !== 'undefined') {
        parent.appendChild(e);
    }
    return e;
}
function openS () {
    let container = createElem('div');

    function setKey (numtxt, keytxt) {
        var kName = createElem('p', container);
        kName.innerHTML = keytxt;
        var key = createElem('a', kName);
        key.setAttribute('style', 'text-decoration:none;');
        key.setAttribute('href', '#');
        key.innerHTML = '[' + GM_getValue(numtxt + 'K') + ']';
        function clk(c) {
            key.setAttribute('style', 'color:#66ffcc; text-decoration:none;');
            key.className = '';
            document.addEventListener("keydown", keyPrs);

        }
        function keyPrs(e) {
            var cde = e.code.replace('Key', '').replace('Digit', '');
            key.innerHTML = '[' + cde + ']';
            key.className = 'mnu';
            key.setAttribute('style', 'text-decoration:none;');
            document.removeEventListener("keydown", keyPrs);
            key.addEventListener('click', function (e) {
                e.preventDefault();
                clk();
            });
            sessionStorage.setItem(numtxt, e.keyCode);
            GM_setValue(numtxt + 'K', cde);
        }
        key.addEventListener('click', function (e) {
            e.preventDefault();
            clk();
        });

    }
    sessionStorage.setItem('menuSwitch', 'on');

    var title = createElem('h3', container);
    title.innerHTML = 'Script Controls';
    setKey('menu', 'Script Controls Menu: ');
    setKey('acc', 'Account Switcher: ');
    setKey('hide', 'Hide Distractions: ');
    setKey('hil', 'Player Highlights: ');

    var zT = createElem('b', container);
    zT.innerHTML = 'Zoom';
    setKey('zIn', 'Zoom In: ');
    setKey('zOut', 'Zoom Out: ');

    var qsT = createElem('b', container);
    qsT.innerHTML = 'Quick Swap<br style = \'line-height: 1.75\'>First Row';
    //row 1 (enabled):
    setKey('q1', 'Slot 1: ');
    setKey('q2', 'Slot 2: ');
    setKey('q3', 'Slot 3: ');
    //row 2 (disabled):
    //var qs2 = createElem('b', container);
    //qs2.innerHTML = 'Second Row';
    //setKey('q4', 'Slot 4: ');
    //setKey('q5', 'Slot 5: ');
    //setKey('q6', 'Slot 6: ');
    //row 3 (disabled)
    //var qs3 = createElem('b', container);
    //qs3.innerHTML = 'Third Row';
    //setKey('q7', 'Slot 7: ');
    //setKey('q8', 'Slot 8: ');
    //setKey('q9', 'Slot 9: ');

    var closeLink = createElem('a', container);
    closeLink.innerHTML = 'Close';
    closeLink.setAttribute('href', '#');
    closeLink.addEventListener('click', function (e) {
        e.preventDefault();
        document.body.removeChild(container);
        sessionStorage.removeItem('menuSwitch');
    });
    document.body.appendChild(container);
}
let styles = createElem('style', document.body);
styles.textContent = `
div.mnu {
position: fixed;
background-color: rgba(16, 16, 16, 0.9);
top: 2%;
left: 38%;
right: 38%;
color: #f0f0f0;
font-size: 12pt;
font-family: Consolas,monospace;
padding: 10px;
text-align: center;
border-radius: 5px;
}
a.mnu:link {
color: #e0e0e0;
}
a.mnu:hover {
color: #f2f2f2;
}
`;
function checkOpen() {
    if (sessionStorage.getItem('menuSwitch') === null) {
        openS();
    }
}
GM_registerMenuCommand('Open Keybind Menu', checkOpen);
if (typeof GM_getValue('menu') === 'undefined'){
    GM_setValue('menu', '84');//default = T
}
document.addEventListener('keydown', function (e){
    if ((e.keyCode == GM_getValue('menu') || e.keyCode == sessionStorage.getItem('menu')) && sessionStorage.getItem('menuSwitch') === null && !XDL.textInputActive) {
        openS();
        if (sessionStorage.getItem('menu') !== null){
            GM_setValue('menu', sessionStorage.getItem('menu'));
            sessionStorage.removeItem('menu');
        }
    }
});
