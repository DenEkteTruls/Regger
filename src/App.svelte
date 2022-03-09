
<script>
	// Import the functions you need from the SDKs you need
	import { initializeApp } from "firebase/app";
	import { getDatabase, ref, onValue, push, update, child} from "firebase/database";
	import { getAnalytics } from "firebase/analytics";
	// TODO: Add SDKs for Firebase products that you want to use
	// https://firebase.google.com/docs/web/setup#available-libraries

	// Your web app's Firebase configuration
	// For Firebase JS SDK v7.20.0 and later, measurementId is optional
	const firebaseConfig = {
  	apiKey: "AIzaSyB10roiWfXShGxXnqF_Do90Iw24tYyTJII",
  	authDomain: "reggie-2870e.firebaseapp.com",
  	databaseURL: "https://reggie-2870e-default-rtdb.firebaseio.com",
  	projectId: "reggie-2870e",
  	storageBucket: "reggie-2870e.appspot.com",
  	messagingSenderId: "870019355288",
  	appId: "1:870019355288:web:fa8623cabc86080ce5b6ed",
  	measurementId: "G-8Y0C5R6FP8"
	};

	// Initialize Firebase
	const app = initializeApp(firebaseConfig);
	const analytics = getAnalytics(app);

	var database = getDatabase(app);

	var user = "";
	var klasse = "";
	var disabled = false;

	import TabList from "./components/TabList.svelte";
	import FilledButton from "./components/FilledButton.svelte";
	import ProgressCircleList from "./components/ProgressCircleList.svelte";

	var groups = [
		{id: 0, title: "General", max: 30, users: [], color: "#3069DF"},
		{id: 1, title: "Meetings", max: 20, users: [], color: "#FC7449"},
		{id: 2, title: "Trip 1", max: 10, users: [], color: "#63F4F7"}
	]

	window.onload = () => {
		if(!disabled) {
			if(localStorage.getItem("du_er_smart->") == undefined) {
				localStorage.setItem("du_er_smart->", false);
			} else {
				if(localStorage.getItem("du_er_smart->") == "true") {
					disabled = true;
				}	
			}
		}
	};

	function changeGroupName(event) {
		let id = event.detail.id;
		let newName = event.detail.newName;

		for(let i = 0; i < groups.length; i++)
		{
			if(groups[i].id == id) {
				groups[i].title = newName;
				break;
			}
		}
	}

	function newGroup() {
		groups = [...groups, {
			id: groups.length,
			title: undefined,
			max: 30,
			users: [],
			color: "#3069DF"
		}];
		console.log(groups);
	}

	function changeGroupColor(event) {
		let id = event.detail.id;
		let new_color = event.detail.new_color;

		for(let i = 0; i < groups.length; i++)
		{
			if(groups[i].id == id) {
				groups[i].color = new_color;
				break;
			}
		}
	}

	function removeGroup(event) {
		let id = event.detail.id;
		groups = groups.filter(group => group.id != id);
	}


	function selectGroup(event) {
		let id = event.detail.id;
		for(let i = 0; i < groups.length; i++)
		{
			if(groups[i].id == id) {
				selectedGroup = groups[i];
				break;
			}
		}
	}

	
	function post(username, klasse, group)
	{
		const a = child(ref(database), "/"+group.title+"/"+username);
		update(a, {klasse});
		localStorage.setItem("du_er_smart->", true);
		disabled = true;
	}

	
	function reg(event)
	{
		let id = event.detail.id;
		let group = event.detail.group;

		if(user != "") {
	
			for(let i = 0; i < groups.length; i++)
			{
				if(groups[i].id == id) {
					groups[i].users = [...groups[i].users, user];
				}
			}
			post(user, klasse, group);
		}
	}


	function username_changed(event)
	{
		user = event.detail.username;
	}


	function klasse_changed(event)
	{
		klasse = event.detail.klasse;
	}


	function check_database()
	{
		var content = {};
		onValue(ref(database, "/"), (snap) => { content = snap.val(); });

		if(content) {
			for(var i = 0; i < groups.length; i++)
			{
				for(const [key, value] of Object.entries(content))
				{
					if(key == groups[i].title) {
						groups[i].users = Object.keys(value);
					}
				}
			}
		}
	}

	
	setInterval(() => {
		check_database();
	}, 250);


</script>


<div class="container">
	<div class="top">
		<div class="left">
			<TabList on:nameChanged={changeGroupName} on:colorChanged={changeGroupColor} on:removeGroup={removeGroup} on:selectedGroup={selectGroup} {groups}/>
			<FilledButton value={"+ CREATE NEW GROUP"} on:newGroup={newGroup}/>
		</div>
		<div class="right">
			<ProgressCircleList on:register={reg} on:username_changed={username_changed} on:klasse_changed={klasse_changed} {disabled} {groups}/>
		</div>
	</div>
	<div class="bottom"></div>
</div>

<style>
	.container {
		display: flex;
		flex-direction: column;
		justify-content: space-evenly;
		align-items: center;
		margin-top: 70px;
	}

	.top {
		display: flex;
		flex-direction: row;
	}

	@media (max-width: 1080px) {
		.container > .top {
			flex-direction: column-reverse;
		}
	}
</style>