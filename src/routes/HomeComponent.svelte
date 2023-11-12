<script>
	import meetups from '../components/Meetups/meetup-store';
	import Header from '../components/UI/Header.svelte';
	import MeetupGrid from '../components/Meetups/MeetupGrid.svelte';

	import EditMeetup from '../components/Meetups/EditMeetups.svelte';
	import MeetupDetail from '../components/Meetups/MeetupDetail.svelte';

	// let meetups = ;

	let editMode;
	let editedId;
	let page = 'overview';
	let pageData = {};

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
		<MeetupGrid
			meetups={$meetups}
			on:showdetails={showDetails}
			on:edit={startEdit}
			on:add={() => {
				editMode = 'edit';
			}}
		/>
	{:else}
		<MeetupDetail id={pageData.id} on:close={closeDetails} />
	{/if}
</main>

<style>
	main {
		margin-top: 5rem;
	}
</style>
