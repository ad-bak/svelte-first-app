<script>
	import meetups from '../components/Meetups/meetup-store';
	import Header from '../components/UI/Header.svelte';
	import MeetupGrid from '../components/Meetups/MeetupGrid.svelte';

	import EditMeetup from '../components/Meetups/EditMeetups.svelte';
	import MeetupDetail from '../components/Meetups/MeetupDetail.svelte';
	import fireApi from '../components/Meetups/vars';
	import LoadingSpinner from '../components/UI/LoadingSpinner.svelte';

	// let meetups = ;

	let editMode;
	let editedId;
	let page = 'overview';
	let pageData = {};
	let isLoading = true;

	fetch(`${fireApi}/meetups.json`)
		.then((res) => {
			if (!res.ok) throw new Error('Failed to fetch meetups.');
			return res.json();
		})
		.then((data) => {
			const loadedMeetups = [];

			for (const key in data) {
				loadedMeetups.push({
					...data[key],
					id: key
				});
			}
			setTimeout(() => {
				isLoading = false;
				meetups.setMeetups(loadedMeetups.reverse());
			}, 1000);
		})
		.catch((err) => {
			isLoading = false;
			console.log(err);
		});

	function savedMeetup(event) {
		editMode = null;
		editedId = null;
	}

	function cancelEdit() {
		editMode = null;
		editedId = null;
	}

	function showDetails(event) {
		page = 'details';
		pageData.id = event.detail;
	}

	function closeDetails() {
		page = 'overview';
		pageData = {};
	}

	function startEdit(event) {
		editMode = 'edit';
		editedId = event.detail;
	}
</script>

<Header />

<main>
	{#if page === 'overview'}
		{#if editMode === 'edit'}
			<EditMeetup id={editedId} on:save={savedMeetup} on:cancel={cancelEdit} />
		{/if}
		{#if isLoading}
			<LoadingSpinner />
		{:else}
			<MeetupGrid
				meetups={$meetups}
				on:showdetails={showDetails}
				on:edit={startEdit}
				on:add={() => {
					editMode = 'edit';
				}}
			/>
		{/if}
	{:else}
		<MeetupDetail id={pageData.id} on:close={closeDetails} />
	{/if}
</main>

<style>
	main {
		margin-top: 5rem;
	}
</style>
