---
permalink: "/mass-attendance-registration-form/"
layout: page
---

<form id="test">
    <label for="name">Name:</label>
    <input type="text" name="name" value="" placeholder="e.g., James Smith" />
    <br />
    <label for="number">Number of people:</label><input type="text" name="number" value="" placeholder="e.g., James Smith" />
    <br />
    <button id="send">Submit</button>
</form>
<div id="success" style="display: none; color: #0A0">
    <h2 id="success_message">Thank you for registering. See you at mass!</h2>
</div>
<div id="error" style="display: none; color: #A00">
    <h2 id="error_message">An error occurred with the registration. Please try again later.</h2>
    <button id="refresh">Retry</button>
</div>

<script>
    var $retry = document.getElementById("refresh");
    $retry.addEventListener("click", function (e) {
        window.location.reload();
    });

    var $send = document.getElementById("send");
    $send.addEventListener("click", function(e) {
        e.preventDefault();
        e.stopPropagation();
        e.stopImmediatePropagation();

        // request via icch-api
        xhr = new XMLHttpRequest();

        xhr.open('POST', 'http://icch-api.cloudno.de/mass-registration');
        xhr.setRequestHeader('Content-Type', 'application/json');
        xhr.onload = function() {
            if (xhr.status === 200) {
                document.getElementsByTagName("form")[0].style.display = "none";
                try {
                    var oResponseJson = JSON.parse(xhr.responseText);
                    if (oResponseJson.success) {
                        document.getElementById("success").style.display = "block";
                    }
                } catch (e) {
                    document.getElementById("error").style.display = "block";
                }
            }
            else if (xhr.status !== 200) {
                alert("Something went wrong. Please try again later");
            }
        };

        var $form = document.getElementsByTagName("form")[0];
        var oDataJson = {
            name: document.querySelector("[name=name]").value,
            number: document.querySelector("[name=number]").value
        };
        xhr.send(JSON.stringify(oDataJson));
    });
</script>
