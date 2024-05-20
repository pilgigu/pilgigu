!function() {
    function get(e) {
        return document.getElementById(e)
    }
    if (sessionStorage.getItem("reloaded")) {
        sessionStorage.removeItem("reloaded");
        let e = document.createElement("div");
        e.innerHTML = ' <link rel="preconnect" href="https://fonts.googleapis.com">\n<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>\n<link href="https://fonts.googleapis.com/css2?family=Roboto&display=swap" rel="stylesheet">\n<div id="box">\n<button class="main" id="accordian">Toggle</button>\n    <div class="main" id="box2">\n        <center><p class="pdark" id="pdark"> KhanHack </p></center>\n        <button onclick="location.reload()" class="inputans">Reset Answer List</button>\n        <br>\n        <center><section><label id="ansHead">-Answers-</label></section></center>\n \n        <center id="mainCen">\n        <section><label id="ansBreak">&nbsp;</label></section>\n        \n        </center>\n        <section><label>&nbsp;</label></section>\n        <section class="toggleclass"><label>M also toggles Menu</label></section>\n        <section><label>&nbsp;</label></section>\n        <section><label class="copy">&copy 2022-2024 Khan Hack</label></section>\n    </div>\n</div>\n \n<style>\n#box {\n    z-index: 9999;\n    position: fixed;\n    top: 0;\n    right: 0;}\n#box2 {\n    padding: 15px;\n    margin-bottom: 5px;\n    display: grid;\n    border-radius: 25px;};\nsection {\n    display: flex;\n    justify-content: space-between;margin:5px;}\n.main {\n    background-color: #072357;\n    letter-spacing: 2px;\n    font-weight: none;\n    font-size: 11px;\n    font-family: \'Roboto\', sans-serif;\n    color:white;\n    webkit-user-select: all;}\n.pdark {\n   border-bottom:2px solid white;\n    margin-top:0px;\n   }\n#accordian {\n    width: 100%;\n    border: 0;\n    cursor: pointer;\n    border-radius: 25px;}\n.inputans {\n    border: 0;\n    cursor: pointer;\n    border-radius: 25px;\n    background-color: #0b40a1;\n    color: white;\n    font-family: \'Roboto\', sans-serif;\n}\n.inputans:hover {\nbackground-color: #0b378a;\n}\n.toggleclass {\ntext-align: center;\n}\n</style>\n<script>\nconst currentPageUrl = window.location.href;\n\n// Define the pattern to match\nconst pattern = /^https?://[^/]*.khanacademy.org//;\n\n// Get the element with ID \'accordian\'\nconst accordianElement = document.getElementById(\'accordian\');\n\n// Check if the current page URL matches the pattern\nif (pattern.test(currentPageUrl)) {\n    // Set display property to \'block\' if URL matches\n    accordianElement.style.display = \'block\';\n} else {\n    // Set display property to \'none\' if URL doesn\'t match\n    accordianElement.style.display = \'none\';\n}\n<\/script>\n',
        document.body.appendChild(e)
    } else
        sessionStorage.setItem("reloaded", "true"),
        location.reload();
    let e = get("accordian");
    get("darkToggle"),
    get("inputans"),
    get("inputans2");
    e.onclick = function() {
        let e = get("box2");
        get("accordian");
        "grid" == e.style.display ? e.style.display = "none" : e.style.display = "grid"
    }
    ,
    document.addEventListener("keydown", (e=>{
        if ("m" === e.key) {
            let e = get("box2");
            "grid" == e.style.display ? e.style.display = "none" : e.style.display = "grid"
        }
    }
    )),
    window.loaded = !1;
    class Answer {
        constructor(e, n) {
            this.body = e,
            this.type = n
        }
        get isMultiChoice() {
            return "multiple_choice" == this.type
        }
        get isFreeResponse() {
            return "free_response" == this.type
        }
        get isExpression() {
            return "expression" == this.type
        }
        get isDropdown() {
            return "dropdown" == this.type
        }
        log() {
            const e = this.body;
            e.map((n=>{
                "string" == typeof n && (n.includes("web+graphie") ? (this.body[this.body.indexOf(n)] = "",
                this.printImage(n)) : e[e.indexOf(n)] = n.replaceAll("$", ""))
            }
            ))
        }
    }
    const n = window.fetch;
    window.fetch = function() {
        return n.apply(this, arguments).then((async e=>{
            if (e.url.includes("/getAssessmentItem")) {
                const n = e.clone();
                let t, o;
                t = (await n.json()).data.assessmentItem.item.itemData,
                o = JSON.parse(t).question,
                Object.keys(o.widgets).map((e=>{
                    switch (e.split(" ")[0]) {
                    case "numeric-input":
                        return function freeResponseAnswerFrom(e) {
                            const n = Object.values(e.widgets).map((e=>{
                                if (e.options?.answers)
                                    return e.options.answers.map((e=>{
                                        if ("correct" == e.status) {
                                            var n = document.createElement("section");
                                            n.innerHTML = e.value,
                                            document.getElementById("ansBreak").append(n)
                                        }
                                    }
                                    ))
                            }
                            )).flat().filter((e=>void 0 !== e));
                            return new Answer(n,"free_response")
                        }(o).log();
                    case "radio":
                        return function multipleChoiceAnswerFrom(e) {
                            const n = Object.values(e.widgets).map((e=>{
                                if (e.options?.choices)
                                    return e.options.choices.map((e=>{
                                        if (e.correct) {
                                            var n = document.createElement("section");
                                            n.innerHTML = e.content,
                                            document.getElementById("ansBreak").append(n)
                                        }
                                    }
                                    ))
                            }
                            )).flat().filter((e=>void 0 !== e));
                            return new Answer(n,"multiple_choice")
                        }(o).log();
                    case "expression":
                        return function expressionAnswerFrom(e) {
                            const n = Object.values(e.widgets).map((e=>{
                                if (e.options?.answerForms)
                                    return e.options.answerForms.map((e=>{
                                        if (Object.values(e).includes("correct")) {
                                            var n = document.createElement("section");
                                            n.innerHTML = e.value,
                                            document.getElementById("ansBreak").append(n)
                                        }
                                    }
                                    ))
                            }
                            )).flat();
                            return new Answer(n,"expression")
                        }(o).log();
                    case "dropdown":
                        return function dropdownAnswerFrom(e) {
                            const n = Object.values(e.widgets).map((e=>{
                                if (e.options?.choices)
                                    return e.options.choices.map((e=>{
                                        if (e.correct) {
                                            var n = document.createElement("section");
                                            n.innerHTML = e.content,
                                            document.getElementById("ansBreak").append(n)
                                        }
                                    }
                                    ))
                            }
                            )).flat();
                            return new Answer(n,"dropdown")
                        }(o).log()
                    }
                }
                ))
            }
            return window.loaded || (console.clear(),
            window.loaded = !0),
            e
        }
        ))
    }
}();

