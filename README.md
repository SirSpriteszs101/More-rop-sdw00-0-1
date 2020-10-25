// ==UserScript==
// @name         StarBreak: Quick Swap (new)
// @namespace    https://gist.github.com/coolguy027/4751b097e1ef9ea178a583f235877c0e
// @version      0.1
// @description  Allows switching with items in your inventory using a single keypress
// @author       tobbez
// @match        http*://*.starbreak.com/*
// @grant        GM_setValue
// @grant        GM_getValue
// @downloadURL  https://gist.github.com/coolguy027/4751b097e1ef9ea178a583f235877c0e/raw/starbreak-quick-swap-new.user.js
// @updateURL    https://gist.github.com/coolguy027/4751b097e1ef9ea178a583f235877c0e/raw/starbreak-quick-swap-new.user.js
// ==/UserScript==
/* jshint -W097 */
'use strict';

//to change keybinds, use the keybind menu script, or change them in storage
//row 1 (enabled):
check('q1', '70');//slot 1 default = F
check('q2', '82');//slot 2 default = R
check('q3', '86');//slot 3 default = V
//row 2 (disabled):
//check('q4', '100');//slot 4 default = Numpad 4
//check('q5', '101');//slot 5 default = Numpad 5
//check('q6', '102');//slot 6 default = Numpad 6
//row 3 (disabled):
//check('q7', '97');//slot 7 default = Numpad 1
//check('q8', '98');//slot 8 default = Numpad 2
//check('q9', '99');//slot 9 default = Numpad 3

function check (item, value) {
    if (typeof GM_getValue(item) === 'undefined'){
        GM_setValue(item, value);
    }
}

var KEY = {
    A: 65,
    TAB: 9,
    LEFT: 37,
    UP: 38,
    RIGHT: 39,
    DOWN: 40,

};

var key_maps = {
    //row 3 (disabled):
    //s7: [KEY.TAB, KEY.DOWN, KEY.DOWN, KEY.A, KEY.TAB], //slot 7
    //s8: [KEY.TAB, KEY.DOWN, KEY.DOWN, KEY.RIGHT, KEY.A, KEY.TAB], //slot 8
    //s9: [KEY.TAB, KEY.DOWN, KEY.DOWN, KEY.RIGHT, KEY.RIGHT, KEY.A, KEY.TAB], //slot 9
    //row 2 (disabled):
    //s4: [KEY.TAB, KEY.DOWN, KEY.A, KEY.TAB], //slot 4
    //s5: [KEY.TAB, KEY.DOWN, KEY.RIGHT, KEY.A, KEY.TAB], //slot 5
    //s6: [KEY.TAB, KEY.DOWN, KEY.RIGHT, KEY.RIGHT, KEY.A, KEY.TAB], //slot 6
    //row 1 (enabled):
    s1: [KEY.TAB, KEY.A, KEY.TAB], //slot 1
    s2: [KEY.TAB, KEY.RIGHT, KEY.A, KEY.TAB], //slot 2
    s3: [KEY.TAB, KEY.RIGHT, KEY.RIGHT, KEY.A, KEY.TAB] //slot 3
};
function sendKey(key) {
    var pd = function dummyPreventDefault() {};
    var ev = { type: 'keydown', keyCode: key, preventDefault: pd };
    XDL.onKey(ev);
}

function sendKeys(keys) {
    keys.forEach(sendKey);
}
function setKey (item, e, map) {
    if ((e.keyCode == GM_getValue(item) || e.keyCode == sessionStorage.getItem(item)) && sessionStorage.getItem('menuSwitch') === null && !XDL.textInputActive) {
        sendKeys(map);
        if (sessionStorage.getItem(item) !== null){
            GM_setValue(item, sessionStorage.getItem(item));
            sessionStorage.removeItem(item);
        }
        map = GM_getValue(item);
    }
}
document.addEventListener('keydown', function (e) {
    //row 1 (enabled):
    setKey('q1', e, key_maps.s1);//slot 1
    setKey('q2', e, key_maps.s2);//slot 2
    setKey('q3', e, key_maps.s3);//slot 3
    //row 2 (disabled):
    //setKey('q4', e, key_maps.s4);//slot 4
    //setKey('q5', e, key_maps.s5);//slot 5
    //setKey('q6', e, key_maps.s6);//slot 6
    //row 3 (disabled):
    //setKey('q7', e, key_maps.s7);//slot 7
    //setKey('q8', e, key_maps.s8);//slot 8
    //setKey('q9', e, key_maps.s9);//slot 9
});
GM_setValue('1', 'adjust the number(s), don\'t remove any quotes, visit \'https://keycode.info\' to find a number');
