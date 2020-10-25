// ==UserScript==
// @name         StarBreak: Hide Distractions (new)
// @namespace    https://gist.github.com/coolguy027/4751b097e1ef9ea178a583f235877c0e
// @version      0.1
// @description  Hides UI and text
// @author       tobbez
// @match        http*://*.starbreak.com/*
// @grant        GM_setValue
// @grant        GM_getValue
// @downloadURL  https://gist.github.com/coolguy027/4751b097e1ef9ea178a583f235877c0e/raw/starbreak-hide-distractions-new.user.js
// @updateURL    https://gist.github.com/coolguy027/4751b097e1ef9ea178a583f235877c0e/raw/starbreak-hide-distractions-new.user.js
// ==/UserScript==

//to change keybinds, use the keybind menu script, or change them in storage

if (typeof GM_getValue('hide') === 'undefined'){
    GM_setValue('hide', '191');//default = /
}
function onLoaded() {
    var uiTextures = new Set();
    var hidingEnabled = false;
    var hpCache = new Map();

    var el = document.createElement('div');

    function toggleHiding () {
        hidingEnabled = !hidingEnabled;
        if (hidingEnabled) {
            el.style.backgroundColor = '#fff';
        } else {
            el.style.backgroundColor = '#000';
        }
    }

    el.setAttribute('style', 'font-size: 9pt; font-family: Consolas, monospace; position: absolute; left: 0; top: 0; border-bottom-right-radius: 5px; color: #fff; padding: 7px; margin: 0; background-color: #000; width: 20px; height: 20px; opacity: 0;');
    document.addEventListener('keydown', function (e) {
        if ((e.keyCode == GM_getValue('hide') || e.keyCode == sessionStorage.getItem('hide')) && sessionStorage.getItem('menuSwitch') === null && !XDL.textInputActive) {
            toggleHiding();
            if (sessionStorage.getItem('hide') !== null){
                GM_setValue('hide', sessionStorage.getItem('hide'));
                sessionStorage.removeItem('hide');
            }
        }
    });
    el.addEventListener('mouseover', function () {
        el.style.opacity = '0.9';
    });
    el.addEventListener('mouseout', function () {
        el.style.opacity = '0';
    });
    document.querySelector('.emscripten_border').setAttribute('style', 'position: absolute; top: 0; left: 0;');
    document.querySelector('.emscripten_border').appendChild(el);

    (function (orig) {
        RenderEngineWebGL._Z21XDL_CreateTextTextureiPKcijS0_ij = function (textureId, fontName, size, color, text, outlineSize, outlineColor) {
            var textStr = Pointer_stringify(text);
            var font = size + 'px ' + Pointer_stringify(fontName);
            var csscolor = TextUtils.loadColorToCSSRGBA(color);

            if (textStr.match(/^\d+\/\d+$/) && textStr.split('/')[1] > 40) {
                var key = [textureId, fontName, size, color, text, outlineSize, outlineColor].join('###');
                if (textureId != 0) {
                    if (hpCache.has(key)) {
                        return hpCache.get(key);
                    }
                }
                var hptid = orig.apply(null, [0, fontName, 24, color, text, outlineSize, outlineColor]);
                hpCache.set(key, hptid);
                return hptid;
            }
            var tid = orig.apply(null, arguments);


            if (!textStr.match(/^\d+\/\d+$/) && !(textStr.match(/^\d+$/) && font == '18px HemiHeadBold' && csscolor == 'rgba(255,0,0,1)') && // Excempt ammo, health and damage taken
                !(textStr.match(/^\d+$/) && ((font == '18px HemiHeadBold' && (csscolor == 'rgba(255,255,255,1)' || csscolor == 'rgba(152,168,152,1)')) || (font == '24px HemiHeadBold' && csscolor == 'rgba(255,0,128,1)') || (font == '26px HemiHeadBold' && csscolor == 'rgba(255,0,128,1)')))) { // And damage done
                uiTextures.add(tid);
            }
            return tid;
        };
    })(RenderEngineWebGL._Z21XDL_CreateTextTextureiPKcijS0_ij);

    (function (orig) {
        RenderEngineWebGL.addTexture = function (drawable) {
            var tid = orig.apply(null, arguments);
            if (drawable.src && drawable.src.match(/\/ui\.0\./)) {
                uiTextures.add(tid);
            }
            return tid;
        };
    })(RenderEngineWebGL.addTexture);

    (function (orig) {
        RenderEngineWebGL._Z15XDL_DrawTextureiiiiiiiiiiiiij9BlendMode = function(texture, sx, sy, sw, sh, dx, dy, dw, dh, rot, rpx, rpy, flip, cm, blendMode) {
            if (hidingEnabled && uiTextures.has(texture)) {
                return;
            }
            return orig.apply(null, arguments);
        };
    })(RenderEngineWebGL._Z15XDL_DrawTextureiiiiiiiiiiiiij9BlendMode);
}

function waitUntilLoaded () {
    if (typeof RenderEngineWebGL == 'undefined') {
        setTimeout(waitUntilLoaded, 100);
        return;
    }
    onLoaded();
}
waitUntilLoaded();
GM_setValue('1', 'adjust the number(s), don\'t remove any quotes, visit \'https://keycode.info\' to find a number');
