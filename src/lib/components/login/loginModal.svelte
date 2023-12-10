<script lang="ts">
	import { browser } from "$app/environment";
	import { base } from "$app/paths";
	import { page } from "$app/stores";

	import Cookies from "js-cookie";
    import Modal from "$lib/components/Modal.svelte";

	import { TextInput, Button, PasswordInput } from "@svelteuidev/core";

	const isIframe = browser && window.self !== window.parent;
	let valueA = "";
	let valueB = "";
	import { afterUpdate, onMount } from "svelte";
	let responseData = ""; // Store the response data here
	let isLoading = false;
	let loginError = false;
	export let showSignUp = false;
	let showForgotPwd = false;

	let emailId = "";
	let fpEmailId = "";
	let mobileNumber = "";
	let password = "";
	let cnfPassword = "";
	let firstName = "";
	let lastName = "";

	let firstDigit = "";
	let secondDigit = "";
	let thirdDigit = "";
	let fourthDigit = "";
	let fifthDigit = "";
	let sixthDigit = "";

	let currentDigit = 0;
	let timer = 60;
	let isTimerRunning = false;
	let inputs;
	let showOtpInputs = false;
	let hideSendOtpBtn = false;
	let showVerifyOtpBtn = false;

	let checkMail = false;
	let sentLink = false;
	let resetLoader = false;
	let resetMailSent = false;

	let countdown;

	let countryCodeMenuFlag = false;

	let googleLoginBtn;
	let clientId = "885560999939-uv51l6cgtbt9t7063r7bahmf74hem9e3.apps.googleusercontent.com";

	let OTPVerified = false;

	let selectedCountry = null;
	let countryOptions = [];

	let countryCodeEnableFlag = true;

	async function fetchCountryData() {
		const response = await fetch("https://restcountries.com/v3.1/all");
		const countries = await response.json();

		countryOptions = countries.map((country) => ({
			label: country.name.common,
			value: country.cca2,
			image: country.flags.png,
		}));
	}
	function handleChange(event) {
		selectedCountry = event.detail;
	}

	const countryCodes = [
		"(+1) United States",
		"(+355) Albania",
		"(+213) Algeria",
		"(+1-684) American Samoa",
		"(+376) Andorra",
		"(+244) Angola",
		"(+1-264) Anguilla",
		"(+672)) Antarctica",
		"(+1-268) Antigua and Barbuda",
		"(+54) Argentina",
		"(+374) Armenia",
		"(+297) Aruba",
		"(+61) Australia",
		"(+43) Austria",
		"(+994) Azerbaijan",
		"(+1-242) Bahamas",
		"(+973) Bahrain",
		"(+880) Bangladesh",
		"(+1-246) Barbados",
		"(+375) Belarus",
		"(+32) Belgium",
		"(+501) Belize",
		"(+229) Benin",
		"(+1-441) Bermuda",
		"(+975) Bhutan",
		"(+591) Bolivia",
		"(+387) Bosnia and Herzegovina",
		"(+267) Botswana",
		"(+55) Brazil",
		"(+1-284) British Virgin Islands",
		"(+673) Brunei",
		"(+359) Bulgaria",
		"(+226) Burkina Faso",
		"(+257) Burundi",
		"(+855) Cambodia",
		"(+237) Cameroon",
		"(+1) Canada",
		"(+238) Cape Verde",
		"(+1-345) Cayman Islands",
		"(+236) Central African Republic",
		"(+235) Chad",
		"(+56) Chile",
		"(+86) China",
		"(+57) Colombia",
		"(+269) Comoros",
		"(+242) Congo",
		"(+506) Costa Rica",
		"(+385) Croatia",
		"(+53) Cuba",
		"(+357) Cyprus",
		"(+420) Czech Republic",
		"(+45) Denmark",
		"(+253) Djibouti",
		"(+1-767) Dominica",
		"(+1-809) Dominican Republic",
		"(+1-670) East Timor",
		"(+593) Ecuador",
		"(+20) Egypt",
		"(+503) El Salvador",
		"(+240) Equatorial Guinea",
		"(+291) Eritrea",
		"(+372) Estonia",
		"(+251) Ethiopia",
		"(+500) Falkland Islands",
		"(+298) Faroe Islands",
		"(+679) Fiji",
		"(+358) Finland",
		"(+33) France",
		"(+596) French Guiana",
		"(+689) French Polynesia",
		"(+262) French Southern Territories",
		"(+241) Gabon",
		"(+220) Gambia",
		"(+995) Georgia",
		"(+49) Germany",
		"(+233) Ghana",
		"(+350) Gibraltar",
		"(+30) Greece",
		"(+299) Greenland",
		"(+1-473) Grenada",
		"(+590) Guadeloupe",
		"(+1-671) Guam",
		"(+502) Guatemala",
		"(+224) Guinea",
		"(+245) Guinea-Bissau",
		"(+592) Guyana",
		"(+509) Haiti",
		"(+504) Honduras",
		"(+852) Hong Kong",
		"(+36) Hungary",
		"(+354) Iceland",
		"(+91) India",
		"(+62) Indonesia",
		"(+98) Iran",
		"(+964) Iraq",
		"(+353) Ireland",
		"(+972) Israel",
		"(+39) Italy",
		"(+1-876) Jamaica",
		"(+81) Japan",
		"(+962) Jordan",
		"(+7) Kazakhstan",
		"(+254) Kenya",
		"(+686) Kiribati",
		"(+850) North Korea",
		"(+82) South Korea",
		"(+965) Kuwait",
		"(+996) Kyrgyzstan",
		"(+856) Laos",
		"(+371) Latvia",
		"(+961) Lebanon",
		"(+266) Lesotho",
		"(+231) Liberia",
		"(+218) Libya",
		"(+423) Liechtenstein",
		"(+370) Lithuania",
		"(+352) Luxembourg",
		"(+853) Macao",
		"(+389) Macedonia",
		"(+261) Madagascar",
		"(+265) Malawi",
		"(+60) Malaysia",
		"(+960) Maldives",
		"(+223) Mali",
		"(+356) Malta",
		"(+692) Marshall Islands",
		"(+596) Martinique",
		"(+222) Mauritania",
		"(+230) Mauritius",
		"(+262) Mayotte",
		"(+52) Mexico",
		"(+691) Micronesia",
		"(+373) Moldova",
		"(+377) Monaco",
		"(+976) Mongolia",
		"(+382) Montenegro",
		"(+1-664) Montserrat",
		"(+212) Morocco",
		"(+258) Mozambique",
		"(+95) Myanmar",
		"(+264) Namibia",
		"(+674) Nauru",
		"(+977) Nepal",
		"(+31) Netherlands",
		"(+599) Netherlands Antilles",
		"(+687) New Caledonia",
		"(+64) New Zealand",
		"(+505) Nicaragua",
		"(+227) Niger",
		"(+234) Nigeria",
		"(+683) Niue",
		"(+672) Norfolk Island",
		"(+1-670) Northern Mariana Islands",
		"(+47) Norway",
		"(+968) Oman",
		"(+92) Pakistan",
		"(+680) Palau",
		"(+970) Palestinian Territories",
		"(+507) Panama",
		"(+675) Papua New Guinea",
		"(+595) Paraguay",
		"(+51) Peru",
		"(+63) Philippines",
		"(+48) Poland",
		"(+351) Portugal",
		"(+1-787) Puerto Rico",
		"(+1-939) Puerto Rico",
		"(+974) Qatar",
		"(+262) Reunion",
		"(+40) Romania",
		"(+7) Russia",
		"(+250) Rwanda",
		"(+290) Saint Helena",
		"(+1-869) Saint Kitts and Nevis",
		"(+1-758) Saint Lucia",
		"(+1-599) Saint Pierre and Miquelon",
		"(+1-784) Saint Vincent and the Grenadines",
		"(+685) Samoa",
		"(+378) San Marino",
		"(+239) Sao Tome and Principe",
		"(+966) Saudi Arabia",
		"(+221) Senegal",
		"(+381) Serbia",
		"(+248) Seychelles",
		"(+232) Sierra Leone",
		"(+65) Singapore",
		"(+421) Slovakia",
		"(+386) Slovenia",
		"(+677) Solomon Islands",
		"(+252) Somalia",
		"(+27) South Africa",
		"(+211) South Sudan",
		"(+34) Spain",
		"(+94) Sri Lanka",
		"(+249) Sudan",
		"(+597) Suriname",
		"(+268) Swaziland",
		"(+46) Sweden",
		"(+41) Switzerland",
		"(+963) Syria",
		"(+886) Taiwan",
		"(+992) Tajikistan",
		"(+255) Tanzania",
		"(+66) Thailand",
		"(+228) Togo",
		"(+690) Tokelau",
		"(+676) Tonga",
		"(+1-868) Trinidad and Tobago",
		"(+216) Tunisia",
		"(+90) Turkey",
		"(+993) Turkmenistan",
		"(+1-649) Turks and Caicos Islands",
		"(+688) Tuvalu",
		"(+1-340) U.S. Virgin Islands",
		"(+256) Uganda",
		"(+380) Ukraine",
		"(+971) United Arab Emirates",
		"(+44) United Kingdom",
		"(+598) Uruguay",
		"(+998) Uzbekistan",
		"(+678) Vanuatu",
		"(+39-06) Vatican City",
		"(+58) Venezuela",
		"(+84) Vietnam",
		"(+1-284) British Virgin Islands",
		"(+1-340) U.S. Virgin Islands",
		"(+681) Wallis and Futuna",
		"(+967) Western Sahara",
		"(+260) Yemen",
		"(+263) Zimbabwe",
	];

	let countryCode = countryCodes[0];

	let searchCountryText = "";

	function searchCountry(arr, query) {
		query = query.toLowerCase();
		return arr.filter((item) => item.toLowerCase().includes(query));
	}

	function toggleContent() {
		const content = document.querySelector(".dropdown-content");
		if (content.style.display == "none" || !content.style.display) {
			content.style.display = "block"; // Display the content
		} else {
			content.style.display = "none"; // Hide the content
		}
	}

	function toggleContents(disp) {
		const content = document.querySelector(".dropdown-content");
		content.style.display = disp;
	}

	$: isSentOtpBtnDisabled = !emailId || !isEmailValid || !mobileNumber || !isMobileValid;

	let showSignupError = false;
	let signUpError = "";
	let isOtpSent = false;
	let isOtpLoading = false;

	function startTimer() {
		timer = 60;
		hideSendOtpBtn = true;
		isTimerRunning = true;
		showVerifyOtpBtn = true;
		clearInterval(countdown);
		countdown = setInterval(() => {
			timer -= 1;
			if (timer === 0) {
				clearInterval(countdown);
				isTimerRunning = false;
			}
		}, 1000);
	}

	function OTPInput() {
		inputs = document.querySelectorAll("#otp > *[id]");
		console.log("testing", inputs);
		for (let i = 0; i < inputs.length; i++) {
			console.log("index", i, inputs[i].value);
			inputs[i].addEventListener("keydown", function (event) {
				if (event.key === "Backspace") {
					inputs[i].value = "";
					if (i !== 0) inputs[i - 1].focus();
				} else {
					if (i === inputs.length - 1 && inputs[i].value !== "") {
						return true;
					} else if (
						(event.keyCode > 47 && event.keyCode < 58) ||
						(event.keyCode > 95 && event.keyCode < 106)
					) {
						inputs[i].value = event.key;
						if (i !== inputs.length - 1) inputs[i + 1].focus();
						event.preventDefault();
					} else if (event.keyCode > 64 && event.keyCode < 91) {
						inputs[i].value = String.fromCharCode(event.keyCode);
						if (i !== inputs.length - 1) inputs[i + 1].focus();
						event.preventDefault();
					}
				}
			});
		}
	}

	$: isEmailValid = /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i.test(emailId);
	$: isFpEmailValid = /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i.test(fpEmailId);
	let otp = "";
	let otpSentError = false;
	let otpVerifyError = false;
	let otpError = "Internal Server Error! Please try again!";

	const handleOTPInput = (event) => {
		otp = event.target.value.replace(/[^0-9]/g, "");
	};

	const btnStyles = {
		backgroundColor: "var(--primary-btn-color) !important",
		color: "var(--primary-btn-text-color) !important",
		"&:disabled": {
			backgroundColor: "var(--primary-btn-color) !important",
			opacity: "0.5 !important",
		},
	};

	// Computed property to check mobile number validity
	$: isMobileValid = /^[0-9]{10}$/i.test(mobileNumber);
	$: passwordsMatch = password === cnfPassword;
	$: isLoginBtnDisabled = !emailId || !isEmailValid || !password;
	$: isSignupBtnDisabled =
		!emailId ||
		!isEmailValid ||
		!isMobileValid ||
		!emailId ||
		!password ||
		!cnfPassword ||
		!firstName ||
		!lastName ||
		!passwordsMatch;
	$: isOTPVerifyEnabled =
		!OTPVerified && emailId && mobileNumber && isMobileValid && isEmailValid && showOtpInputs;

	$: isVerifyOtpBtnDisabled = !otp || otp.length != 6;

	function renderSignInButton() {
		window.google.accounts.id.initialize({
			client_id: clientId,
			callback: onGoogleAuthSuccess,
			prompt: "select_by",
			scope: "email",
		});
		window.google.accounts.id.renderButton(googleLoginBtn, {
			text: "signin",
			shape: "square",
			size: "large",

			theme: "white",
		});

		window.google.accounts.id.prompt();
	}

	let filteredItems = (searchText) => {
		return countryCodes.filter((item) => item.toLowerCase().includes(searchText.toLowerCase()));
	};

	async function onGoogleAuthSuccess(jwtCredentials) {
		const profileData = JSON.parse(atob(jwtCredentials.credential.split(".")[1]));
		let email = profileData.email;
		let params = {
			email: email,
			username: email,
			password: "",
		};
		await fetch("https://backend.immigpt.ai/signup?isGoogleSignIn=true", {
			method: "POST",
			headers: {
				"Content-Type": "application/json",
			},
			body: JSON.stringify(params),
		})
			.then(async (response) => {
				if (response.status == 200) {
					let data = await response.json();
					var idToken = jwtCredentials.credential;
					let payload = parseJwt(data.token);
					const expirationTime = new Date();
					expirationTime.setTime(expirationTime.getTime() + 7 * 24 * 60 * 60 * 1000);
					Cookies.set("token", idToken, { expires: expirationTime });
					Cookies.set("email", profileData.email, { expires: expirationTime });
					Cookies.set("name", profileData.name, { expires: expirationTime });
					Cookies.set("userId", payload.userId);
					Cookies.set("Google-Auth", "true", { expires: expirationTime });
					window.location.href = "/";
				}
			})
			.catch((error) => {
				console.log("error.response1", error);
			});
	}

	function onSignIn(googleUser) {
		let profile = googleUser.getBasicProfile();
		let fullName = profile.getName();
		let email = profile.getEmail();
		let imageUrl = profile.getImageUrl();
		const expirationTime = new Date();
		expirationTime.setTime(expirationTime.getTime() + 1 * 60 * 60 * 1000);
		Cookies.set("token", profile, { expires: expirationTime });
		Cookies.set("email", email, { expires: expirationTime });
		Cookies.set("name", fullName, { expires: expirationTime });
		Cookies.set("imageUrl", imageUrl, { expires: expirationTime });
		window.location.href = "/";
	}

	async function Login() {
		try {
			isLoading = true; 

			let loginData = {
				email: emailId,
				password: password,
			};
			await fetch("https://backend.immigpt.ai/login", {
				method: "POST",
				headers: {
					"Content-Type": "application/json",
				},
				body: JSON.stringify(loginData),
			})
				.then(async (response) => {
					if (response.status == 200) {
						let data = await response.json();
						const expirationTime = new Date();
						expirationTime.setTime(expirationTime.getTime() + 1 * 60 * 60 * 1000);
						Cookies.set("token", data.token, { expires: expirationTime });
						let payload = parseJwt(data.token);
						Cookies.set("name", payload.username, { expires: expirationTime });
						Cookies.set("email", payload.email, { expires: expirationTime });
						Cookies.set("userId", payload.userId, { expires: expirationTime });
						Cookies.set("phoneNumber", payload.phoneNumber, { expires: expirationTime });
						window.location.href = "/";
					} else if (response.status === 401) {
						loginError = true;
						password = "";
					}
				})
				.catch((error) => {
					console.log("error.response1", error);
					// if (error.response.status == 401) {
					// 	loginError = true;
					// }
					// responseData = `Error: ${error.message}`;
					// isLoading = false;
				});
		} catch (error) {
			// Handle any network or other errors;
			console.log("error.response", error);
			if (error.response.status == 401) {
				loginError = true;
			}
			responseData = `Error: ${error.message}`;
		} finally {
			isLoading = false; // Reset loading flag whether the request succeeds or fails
		}
	}

	function toggleSignup() {
		showSignUp = true;
		timer = 60;
		isTimerRunning = false;
		showOtpInputs = false;
		hideSendOtpBtn = false;
		showVerifyOtpBtn = false;

		OTPVerified = false;
		emailId = "";
		mobileNumber = "";
		firstName = "";
		lastName = "";
		password = "";
		cnfPassword = "";
		otp = "";
		renderSignInButton();
	}

	function toggleLogin() {
		showSignUp = false;
		showForgotPwd = false;
		firstName = "";
		lastName = "";
		password = "";
		cnfPassword = "";
		otp = "";
		// renderSignInButton();
	}

	function parseJwt(token) {
		var base64Payload = token.split(".")[1];
		// var payload = Buffer.from(base64Payload, 'base64');
		// return JSON.parse(payload.toString());
		var payload = JSON.parse(atob(base64Payload));
		return payload;
	}

	async function handleSignUp() {
		try {
			isLoading = true; // Set loading flag while making the API call

			let signUpData = {
				email: emailId,
				password: password,
				firstName: firstName,
				lastName: lastName,
				username: firstName + " " + lastName,
				phoneNumber: countryCode.split("(")[0].trim() + mobileNumber,
			};
			await fetch("https://backend.immigpt.ai/signup", {
				method: "POST",
				headers: {
					"Content-Type": "application/json",
				},
				body: JSON.stringify(signUpData),
			})
				.then(async (response) => {
					let data = await response.json();
					if (response.status == 200) {
						const expirationTime = new Date();
						expirationTime.setTime(expirationTime.getTime() + 1 * 60 * 60 * 1000);
						Cookies.set("token", data.token, { expires: expirationTime });
						let payload = parseJwt(data.token);
						Cookies.set("name", payload.username, { expires: expirationTime });
						Cookies.set("email", payload.email, { expires: expirationTime });
						Cookies.set("phoneNumber", payload.phoneNumber, { expires: expirationTime });
						Cookies.set("userId", payload.userId);
						window.location.href = "/";
					} else {
						showSignupError = true;
						signUpError = data.message ? data.message : "Unable to sign up..";
					}
					isLoading = false;
				})
				.catch((error) => {
					console.log("error.response", error.response.status == 401);
					if (error.response.status == 400 || error.response.status == 401) {
						showSignupError = true;
						signUpError = error.message;
					} else {
						signUpError = "Something went wrong. Please try again!!";
					}
					isLoading = false;
				});
		} catch (error) {
			// Handle any network or other errors;
			console.log("error.response", error.response.status == 401);
			if (error.response.status == 401) {
				loginError = true;
			}
			responseData = `Error: ${error.message}`;
		} finally {
			isLoading = false; // Reset loading flag whether the request succeeds or fails
		}
	}

	async function sendOtp() {
		try {
			isOtpSent = true;
			otpSentError = false;
			countryCodeEnableFlag = false;
			let otpData = {
				phoneNumber:
					countryCode.split(" ")[0].replace("(", "").replace(")", "").trim() + mobileNumber,
			};
			await fetch("https://backend.immigpt.ai/generateOTP", {
				method: "POST",
				headers: {
					"Content-Type": "application/json",
				},
				body: JSON.stringify(otpData),
			})
				.then(async (response) => {
					if (response.status == 200) {
						showOtpInputs = true;
						timer = 60;
						startTimer();
						// setTimeout(() => {
						// 	OTPInput();
						// }, 1000);
					} else {
						console.log("error", response);
						otpSentError = true;
						otpError = "Internal server error! Please try again";
					}
					isOtpSent = false;
				})
				.catch((error) => {
					console.log("error.response", error.response.status == 401);
					isOtpSent = false;
					otpSentError = true;
					otpError = "Internal server error! Please try again";
				});
		} catch (error) {
			console.log(error);
			isOtpSent = false;
			otpSentError = true;
			otpError = "Internal server error! Please try again";
		}
	}

	function resendOtp() {
		sendOtp();
		timer = 60;
		startTimer();
		otpVerifyError = false;
		otp = "";
	}

	async function verifyOtp() {
		try {
			otpVerifyError = false;

			isOtpLoading = true;
			let otpData = {
				phoneNumber:
					countryCode.split(" ")[0].replace("(", "").replace(")", "").trim() + mobileNumber,
				otp: otp,
			};
			await fetch("https://backend.immigpt.ai/verifyOTP", {
				method: "POST",
				headers: {
					"Content-Type": "application/json",
				},
				body: JSON.stringify(otpData),
			})
				.then(async (response) => {
					if (response.status == 200) {
						OTPVerified = true;
						isOtpLoading = false;
					} else {
						console.log("error", response);
						otpVerifyError = true;
						otpError = "Invalid OTP. Please try again!";
						isOtpLoading = false;
					}
				})
				.catch((error) => {
					console.log("error.response", error.response.status == 401);
					otpVerifyError = true;
					otpError = "Invalid OTP. Please try again!";
					isOtpLoading = false;
				});
		} catch (error) {
			console.log(error);
			otpVerifyError = true;
			otpError = "Internal server error! Please try again";
			isOtpLoading = false;
		}
	}

	function toggleForgotPwd() {
		showForgotPwd = true;
	}

	function openEmailApp() {
		window.open("https://mail.google.com", "_blank");
	}

	function NavigateToLogin() {
		// showSignUp = false;
		// showForgotPwd = false;
		// resetMailSent = false;
		// goto("/");
		firstName = "";
		lastName = "";
		password = "";
		cnfPassword = "";
		window.location.href = "/";
	}

	async function forgotPassword() {
		if (!fpEmailId) {
			checkMail = true;
		} else {
			resetLoader = true;

			await fetch("https://backend.immigpt.ai/sendResetPasswordMail?email=" + fpEmailId, {
				method: "POST",
				headers: {
					"Content-Type": "application/json",
				},
			})
				.then(async (response) => {
					if (response.status == 200) {
						sentLink = true;
						showForgotPwd = false;
						resetMailSent = true;
						checkMail = false;
						resetLoader = false;
					}
					resetLoader = false;
				})
				.catch((error) => {
					console.log("error.response", error.response.status == 401);
					resetLoader = false;
				});
		}
	}

	let dropCon = "";

	onMount(() => {
		//renderSignInButton();
		fetchCountryData();
		document.addEventListener("click", (event) => {
			// const dropCon = document.getElementById("country-code");
			if (!dropCon?.contains(event.target)) {
				toggleContents("none");
			}
		});

		if (showSignUp) {
			toggleSignup();
		}
	});

	afterUpdate(() => {
		dropCon = document.querySelector(".country-code");
	});
</script>

<Modal>
	<div class="wrapper">
		<div class="header">
			<p class="app-title">ImmiGPT</p>
			{#if showSignUp && !showForgotPwd && !resetMailSent}
				<p class="login-text">Create your account</p>
				<p class="welcome-text">Enjoy benefits of ImmiGPT in seconds</p>
			{:else if !showSignUp && !showForgotPwd && !resetMailSent}
				<p class="login-text">Login to your account</p>
				<p class="welcome-text">Welcome back ! Please Enter your details</p>
			{:else if showForgotPwd && !resetMailSent}
				<p class="login-text">Forgot Password</p>
				<p class="welcome-text">No worries, we’ll send you reset instructions.</p>
			{:else if resetMailSent}
				<p class="login-text">Check your mail</p>
				<p class="gray-text">We sent a password reset link to</p>
				<p class="welcome-text">{fpEmailId}</p>
				<button class="login-btn" on:click={openEmailApp}> Open Email App</button>
				<div class="signin-text center">
					<p class="no-account-text">Didn't Recieve the email?</p>
					<button class="signup-text" on:click={forgotPassword}>Click to resend</button>
				</div>
				<div class="signin-text center">
					<button on:click={NavigateToLogin} class="signup-text  back-btn">
						<img src="/assets/icons/back-icon-black.svg" alt="" />
						<p>Back to Login</p></button
					>
				</div>
			{/if}
		</div>
		{#if !resetMailSent}
			<div class="login-popup">
				{#if $page.data.requiresLogin}
					<p
						class="px-4 text-lg font-semibold leading-snug text-gray-800 sm:px-12"
						style="text-wrap: balance;"
					>
						Please Sign in with Hugging Face to continue
					</p>
				{/if}

				{#if !showSignUp && !showForgotPwd && !resetMailSent}
					<div class="input-fields">
						<div>
							<TextInput bind:value={emailId} label="Email Address" placeholder="Your email" />
						</div>
						{#if (emailId && !isEmailValid) || checkMail}
							<p class="error">{!checkMail ? "Enter a valid Email Id" : "Email Id is mandatary"}</p>
						{/if}
						<div>
							<PasswordInput
								bind:value={password}
								type="password"
								label="Password"
								placeholder="Password"
							/>
						</div>
						<div class="signin-text">
							<!-- {#if !sentLink && !resetLoader} -->
							<button class="signup-text" on:click={toggleForgotPwd}>Forgot password ?</button>
							<!-- {:else if !resetLoader}
							<p style="color: green;">
								We have sent a reset password link <br /> to your mail. Please check
							</p>
						{:else}
							<p style="color: green;">Sending..</p>
						{/if} -->
						</div>
						<div class="login-button">
							<!-- <button
								class="login-btn {isLoginBtnDisabled ? 'disabled' : ''}"
								on:click={Login}
								disabled={isLoginBtnDisabled}
							>
								Login</button
							> -->
							<Button
								className="login-btn"
								override={btnStyles}
								bind:loading={isLoading}
								on:click={Login}
								disabled={isLoginBtnDisabled}
								fullSize={true}
								ripple>Login</Button
							>
						</div>
						<div class="signin-text center">
							<p class="no-account-text">Don't have an account?</p>
							<button class="signup-text" on:click={toggleSignup}>Sign up</button>
						</div>
					</div>
				{:else if showSignUp && !showForgotPwd && !resetMailSent}
					<div class="input-fields">
						<div>
							<TextInput
								bind:value={emailId}
								disabled={showOtpInputs || OTPVerified}
								label="Email"
								placeholder="Email"
								className="loginModal"
							/>
						</div>
						{#if emailId && !isEmailValid}
							<p class="error">Enter a valid Email Id</p>
						{/if}
						<div class="sendOTP">
							<p style="color: #000;">Mobile Number</p>
							<div class="mobile-number-section">
								<div class="country-code" id="country-code">
									<div class="dropdown">
										<div
											class="countryCodeWrap"
											on:click={() => (countryCodeEnableFlag ? toggleContent() : "")}
										>
											<span
												class="coutryCodeText"
												style={countryCodeEnableFlag ? "" : "color: grey;"}
											>
												{countryCode.split(" ")[0].replace("(", "").replace(")", "")}
											</span>
										</div>
										<div class="dropdown-content">
											<TextInput
												on:click={() => (countryCodeMenuFlag = true)}
												bind:value={searchCountryText}
												on:change={() => searchCountry(countryCodes, searchCountryText)}
												placeholder="Search country"
											/>
											<div
												class="scrollbar-custom"
												style="max-height: 100px; overflow-y:scroll; padding: 8px; display: flex; flex-direction: column; align-items: baseline; gap: 8px;"
											>
												<!-- {#each countryCodes as countryCodeText} -->
												{#each searchCountry(countryCodes, searchCountryText) as countryCodeText}
													<button
														class="selectCode"
														on:click={() => {
															countryCode = countryCodeText;
															toggleContents("none");
														}}
													>
														{countryCodeText}
													</button>
												{/each}
											</div>
										</div>
									</div>
								</div>
								<div style="width:100%;">
									<TextInput
										bind:value={mobileNumber}
										placeholder="Mobile Number"
										disabled={showOtpInputs || OTPVerified}
									/>
								</div>
							</div>

							{#if mobileNumber && !isMobileValid}
								<p class="error">Enter a valid mobile number</p>
							{/if}
							{#if !hideSendOtpBtn}
								<div class="login-button">
									<!-- <Button
									size="xs"
									disabled={isSentOtpBtnDisabled}
									ripple
									color="#3b82f6"
									on:click={sendOtp}>Send OTP</Button -->

									<Button
										className="login-btn otp-btn {isSentOtpBtnDisabled ? 'disabled' : ''}"
										on:click={sendOtp}
										disabled={isSentOtpBtnDisabled}
										override={btnStyles}
										fullSize={true}
										ripple
										bind:loading={isOtpSent}
									>
										Send OTP</Button
									>
								</div>
							{/if}
							{#if otpSentError}
								<p class="error">{otpError}</p>
							{/if}
						</div>
						{#if showOtpInputs && !OTPVerified}
							<div class="otp-body">
								<p class="enter-otp-small">Enter OTP</p>
								<div id="otp" class="inputs">
									<!-- <input
									bind:value={firstDigit}
									class="rounded"
									type="text"
									id="first"
									maxlength="1"
								/>
								<input
									bind:value={secondDigit}
									class="rounded"
									type="text"
									id="second"
									maxlength="1"
								/>
								<input
									bind:value={thirdDigit}
									class="rounded"
									type="text"
									id="third"
									maxlength="1"
								/>
								<input
									bind:value={fourthDigit}
									class="rounded"
									type="text"
									id="fourth"
									maxlength="1"
								/>
								<input
									bind:value={fifthDigit}
									class="rounded"
									type="text"
									id="fifth"
									maxlength="1"
								/>
								<input
									bind:value={sixthDigit}
									class="rounded"
									type="text"
									id="sixth"
									maxlength="1"
								/> -->
									<input
										class="otp-field"
										type="text"
										on:input={handleOTPInput}
										bind:value={otp}
										maxlength="6"
									/>
								</div>

								{#if isTimerRunning}
									<p class="timer-text">
										Didn't Receive? Resend in <span class="gold-text">{timer} s</span>
									</p>
								{/if}
								{#if !isTimerRunning}
									<div class="signin-text center">
										<p class="no-account-text">Didn't Recieve?</p>
										<button class="signup-text" on:click={resendOtp}>Resend OTP</button>
									</div>
								{/if}
								<!-- {#if isTimerRunning} -->
								<div class="login-button">
									<Button
										className="login-btn otp-btn {isVerifyOtpBtnDisabled ? 'disabled' : ''}"
										on:click={verifyOtp}
										disabled={isVerifyOtpBtnDisabled}
										fullSize={true}
										override={btnStyles}
										ripple
										bind:loading={isOtpLoading}
									>
										Verify OTP</Button
									>
								</div>
								<!-- {/if} -->
								{#if otpVerifyError}
									<p class="error">{otpError}</p>
								{/if}
							</div>
						{/if}
						{#if OTPVerified}
							<div>
								<TextInput bind:value={firstName} label="First Name" placeholder="First Name" />
							</div>
							<div>
								<TextInput bind:value={lastName} label="Last Name" placeholder="Last Name" />
							</div>
							<div>
								<PasswordInput
									bind:value={password}
									type="password"
									label="Password"
									placeholder="Password"
								/>
							</div>
							<div>
								<TextInput
									bind:value={cnfPassword}
									type="password"
									label="Confirm Password"
									placeholder="Confirm Password"
								/>
							</div>
							{#if password && cnfPassword && !passwordsMatch}
								<p class="error">Passwords do not match</p>
							{/if}
							<div class="login-button">
								<!-- <button
							type="submit"
							class="signup-btn mt-2 rounded-full bg-black px-5 py-2 text-lg font-semibold text-gray-100 transition-colors hover:bg-primary-500 {isSignupBtnDisabled
								? 'disabled'
								: ''}"
							on:click={handleSignUp}
							disabled={isSignupBtnDisabled}
						>
							{isLoading ? "Loading..." : "Sign up"}
						</button> -->
								<!-- <Button
								bind:loading={isLoading}
								color="#3b82f6"
								on:click={handleSignUp}
								disabled={OTPVerified ? isSignupBtnDisabled : !isOTPVerifyEnabled}
								ripple>{OTPVerified ? "Sign up" : "Verify"}</Button
							> -->

								<Button
									className="login-btn otp-btn {OTPVerified
										? isSignupBtnDisabled
										: !isOTPVerifyEnabled
										? 'disabled'
										: ''}"
									on:click={handleSignUp}
									fullSize={true}
									ripple
									override={btnStyles}
									bind:loading={isLoading}
									disabled={!OTPVerified || isSignupBtnDisabled}
									>{OTPVerified ? "Sign up" : "Verify"}</Button
								>
							</div>
							{#if showSignupError}
								<div class="signup-error">
									<p class="error">{signUpError}</p>
								</div>
							{/if}
						{/if}
						<div class="signin-text center">
							<p class="no-account-text">Already have an account?</p>
							<button on:click={toggleLogin} class="signup-text">Login</button>
						</div>
					</div>
				{:else if showForgotPwd && !resetMailSent}
					<div class="input-fields">
						<div>
							<TextInput
								bind:value={fpEmailId}
								label="Email Address"
								placeholder="Ex: abc@gmail.com"
							/>
						</div>
						{#if fpEmailId && !isFpEmailValid}
							<p class="error">Enter a valid Email Id</p>
						{/if}
						<div class="login-button">
							<Button
								className="login-btn otp-btn {!isFpEmailValid ? 'disabled' : ''}"
								on:click={forgotPassword}
								disabled={!isFpEmailValid}
								fullSize={true}
								ripple
								bind:loading={resetLoader}
								override={btnStyles}
							>
								{resetLoader ? "Sending.." : "Reset Password"}</Button
							>
						</div>
						<div class="signin-text center">
							<button on:click={toggleLogin} class="signup-text  back-btn">
								<img src="/assets/icons/back-icon-black.svg" alt="" />
								<p>Back to Login</p></button
							>
						</div>
					</div>
				{/if}
				{#if !resetMailSent}
					<div class="google-button">
						<div bind:this={googleLoginBtn} />
						<!-- <div class="g-signin2" data-onsuccess={onSignIn} data-onfailure="onSignInFailure" /> -->
					</div>
				{/if}
				{#if loginError}
					<p class="error">Failed while logging in</p>
					<!-- Display the error message in red -->
				{/if}

				<form
					action="{base}/{$page.data.requiresLogin ? 'login' : 'settings'}"
					target={isIframe ? "_blank" : ""}
					method="POST"
					class="flex w-full flex-col items-center gap-2"
				>
					
				</form>
			</div>
		{/if}
	</div>
</Modal>

<style>
	/* .login-container {
		min-width: 400px;
	} */

	.dropdown {
		position: relative;
		display: inline-block;
	}

	.dropdown-content {
		display: none;
		position: absolute;
		z-index: 15;
		padding: 8px;
		background-color: #fff;
		box-shadow: 0px 8px 16px 0px rgba(0, 0, 0, 0.2);
		border-radius: 8px;
		width: 300px;
		color: #222;
	}

	.wrapper {
		width: 100%;
		height: 100%;
		display: flex;
		align-items: center;
		justify-content: center;
		flex-direction: column;
		gap: 32px;
	}

	.login-popup {
		width: 440px;
		/* max-height: 500px; */
		overflow-y: auto;
		top: 255px;
		left: 500px;
		padding: 32px 40px 32px 40px;
		border-radius: 12px;
		border: 1px;
		gap: 16px;
		box-shadow: 0px 1px 12px 0px rgba(0, 0, 0, 0.08);
		background: white;
		/* background: var(--primary-background-color); */
	}

	.login-popup::-webkit-scrollbar {
		width: 0 !important;
	}
	.login-popup {
		overflow: -moz-scrollbars-none;
	}
	.login-popup {
		-ms-overflow-style: none;
	}

	.coutryCodeText {
		/* color: var(--primary-text-color); */
		color: #222;
	}

	.app-title {
		font-family: Inter;
		font-size: 24px;
		font-weight: 700;
		line-height: 29px;
		letter-spacing: 0em;
		text-align: left;
		text-align: center;
		color: #000;
	}

	.login-text {
		font-family: Inter;
		font-size: 32px;
		font-weight: 700;
		line-height: 39px;
		letter-spacing: 0em;
		text-align: left;
		text-align: center;
		color: #000;
	}

	.welcome-text {
		font-family: Inter;
		font-size: 16px;
		font-weight: 400;
		line-height: 19px;
		letter-spacing: 0em;
		text-align: center;
		color: #000;
	}

	.header {
		display: flex;
		flex-direction: column;
		gap: 12px;
		color: var(--primary-text-color, black);
	}
	.input-fields {
		display: flex;
		flex-direction: column;
		gap: 16px;
	}

	.login-btn {
		width: 100%;
		background: var(--primary-btn-color);
		color: var(--primary-btn-text-color);
		width: 360px;
		height: 44px;
		padding: 12px 16px 12px 16px;
		border-radius: 8px;
		gap: 8px;
	}

	.otp-btn {
		margin-top: 12px;
	}

	.otp-field {
		border: 1px solid rgba(225, 225, 225, 1);
		width: 100%;
		border-radius: 8px;
		padding: 6px;
	}

	.error {
		color: red;
		margin-top: 5px;
		text-align: center;
	}

	.signin-text {
		display: flex;
		gap: 12px;
		justify-content: flex-end;
		text-decoration: none;
	}

	.signin-text.center {
		justify-content: center;
	}

	.signup-text {
		color: black;
        background: none;
	}

	.signup-btn.disabled {
		background-color: var(--primary-btn-disabled-color);
		cursor: not-allowed;
	}

	.login-popup {
		min-width: 400px;
	}
	.login-button {
		display: flex;
		justify-content: center;
		align-items: center;
	}
	.otp-body {
		display: flex;
		flex-direction: column;
		gap: 20px;
	}

	#otp {
		display: flex;
		align-items: center;
		gap: 8px;
	}

	#otp .rounded {
		width: 50px;
		height: 50px;
		border: 2px solid #ccc;
		/* border-radius: 50%; */
		font-size: 16px;
		text-align: center;
		outline: none;
		padding: 0;
	}
	.sendOTP {
		display: flex;
		flex-direction: column;
		gap: 10px;
	}

	.mobile-number-section {
		display: flex;
		gap: 8px;
	}

	.container-2 {
		display: flex;
		flex-direction: column;
		gap: 12px;
	}

	.country-code {
		max-width: 90px;
	}

	.selectCode {
		display: flex;
		width: 100%;
		text-align: left;
	}

	.google-button {
		display: flex;
		width: 100%;
		margin-top: 12px;
		justify-content: center;
		align-items: center;
	}

	.login-btn.disabled {
		background-color: var(--primary-btn-color);
		cursor: not-allowed;
		opacity: 0.5 !important;
	}

	.timer-text {
		color: rgba(146, 146, 146, 1);
		text-align: end;
	}

	.no-account-text {
		color: rgba(146, 146, 146, 1);
	}

	.back-btn {
		display: flex;
		gap: 8px;
		align-items: center;
		justify-content: center;
	}

	.gray-text {
		color: rgba(0, 0, 0, 0.54);

		text-align: center;
		font-family: Inter;
		font-size: 16px;
		font-style: normal;
		font-weight: 400;
		line-height: 24px;
	}

	.countryCodeWrap {
		display: flex;
		justify-content: center;
		align-items: center;
		border-radius: 8px;
		border: #222 solid 1px;
		padding: 4px;
		width: 80px;
		overflow: hidden;
		cursor: pointer;
	}

	.svelteui-InputWrapper-root .svelteui-InputWrapper-label,
	.svelteui-Tab-label label {
		color: #000 !important;
	}

	.svelteui-InputWrapper-root .svelteui-Input-root .svelteui-Input-input {
		background-color: #fff !important;
		border: 1px solid #605f5f;
		color: #000;
	}

	@media (max-width: 600px) {
		.login-popup {
			min-width: 90vw;
			max-width: 90vw;
			padding: 16px;
		}

		.app-title {
			font-size: 24px;
		}

		.login-text {
			font-size: 18px;
		}

		.welcome-text,
		.signup-text,
		.no-account-text {
			font-size: 16px;
		}
	}

	@media (max-width: 400px) {
		.login-popup {
			min-width: 90vw;
			max-width: 90vw;
		}
	}
</style>
