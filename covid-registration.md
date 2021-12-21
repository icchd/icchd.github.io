---
permalink: "/mass-attendance-registration-form/"
layout: page
---

<form style="display: none" id="test">
    Registration is <span style="font-weight: bold; color: #0A0">open</span> for <span id="massDate" style="font-weight: bold">next Sunday's mass</span>.
    <br />
    <span style="color: #999">Please enter one name and the total number of attendees we can expect at mass. We will use the data you have entered solely to check attendance on mass day. We will not retain these data or forward them to third parties.</span>
    <br />
    <br />
    <ul>
        <li>
            <label for="name">Name:</label>
            <input type="text" name="name" value="" placeholder="e.g., John Doe" />
        </li>
        <li>
            <label for="number">Total number of attendees:</label><input type="number" name="number" min="1" max="10" value="1" />
            <span style="color: #0A0"><span id="availableSeats" style="font-weight: bold">0</span> seats available.</span>
        </li>
    </ul>
    <button id="send">Submit</button>
</form>
<div id="availability" style="color: #AAA">
    <h3 id="availability_message">Loading, please wait...</h3>
</div>
<div id="progress" style="display: none; color: #AAA">
    <h3 id="progress_message">Registration in progress...</h3>
</div>
<div id="success" style="display: none; color: #0A0">
    <h3 id="success_message">Thank you for registering. See you at mass!</h3>
</div>
<div id="error" style="display: none; color: #A00">
    <h3 id="error_message">An error occurred. Please try again later.</h3>
    <button id="refresh">Retry</button>
</div>

<script>
    if (window.location.protocol.indexOf("https") >= 0) {
        window.location.protocol = "http"; // API does not support https unfortunately!
    }

    var $retry = document.getElementById("refresh");
    $retry.addEventListener("click", function (e) {
        window.location.reload();
    });

    var $send = document.getElementById("send");
    $send.addEventListener("click", function(e) {
        e.preventDefault();
        e.stopPropagation();
        e.stopImmediatePropagation();

        document.getElementsByTagName("form")[0].style.display = "none";
        document.getElementById("progress").style.display = "block";

        // request via icch-api
        xhr = new XMLHttpRequest();

        xhr.open('POST', 'http://icch-api.cloudno.de/mass-registration');
        xhr.setRequestHeader('Content-Type', 'application/json');
        xhr.onload = function() {
            if (xhr.status === 200) {
                document.getElementById("progress").style.display = "none";

                try {
                    var oResponseJson = JSON.parse(xhr.responseText);
                    if (oResponseJson.success) {
                        document.getElementById("success").style.display = "block";
                        if (oResponseJson.message) {
                            document.getElementById("success_message").innerText = oResponseJson.message;
                        }
                    } else {
                        document.getElementById("error").style.display = "block";
                        if (oResponseJson.message) {
                            document.getElementById("error_message").innerText = oResponseJson.message;
                        }
                    }
                } catch (e) {
                    document.getElementById("error").style.display = "block";
                    if (oResponseJson.message) {
                        document.getElementById("error_message").innerText = oResponseJson.message;
                    }
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


    var xhr = new XMLHttpRequest();
    xhr.open('GET', 'http://icch-api.cloudno.de/mass-registration-check');
    
    xhr.onload = function() {
        document.getElementById("availability").style.display = "none";

        if (xhr.status === 200) {
            try {
                var oResponseJson = JSON.parse(xhr.responseText);
                if (oResponseJson.success) {
                    if (oResponseJson.status === "closed") {
                        document.getElementById("error_message").innerText = "We are sorry, the registration for " + oResponseJson.date + " is currently closed. We will open it if enough volunteers are available to support the service on that date.";
                        document.getElementById("error").style.display = "block";
                        return;
                    }
                    if (oResponseJson.places <= 0) {
                        document.getElementById("error_message").innerText = "We are sorry, all available seats were booked for " + oResponseJson.date + ". Please come back later to this page to register for the next mass.";
                        document.getElementById("error").style.display = "block";
                        return;
                    }

                    document.getElementById("massDate").innerText = oResponseJson.date;
                    document.getElementById("availableSeats").innerText = oResponseJson.places;
                    document.getElementsByTagName("form")[0].style.display = "block";
                } else {
                    document.getElementById("error").style.display = "block";
                    if (oResponseJson.message) {
                        document.getElementById("error_message").innerText = oResponseJson.message;
                    }
                }
            } catch (e) {
                document.getElementById("error").style.display = "block";
                if (console) { console.log(e); }
            }
        } else {
            document.getElementById("error").style.display = "block";
        }
    };
xhr.send();
</script>
