<script>
	import client from "./graphql-client";
	import gql from "graphql-tag";
	import 'ag-grid-community/styles/ag-grid.css';
  	import 'ag-grid-community/styles/ag-theme-alpine.css';
	import AgGridSvelte from 'ag-grid-svelte';
	let user = {
		userName: "",
		departmentName: "Employee"
	}
	let departmentNamesAGList = ['Employee', 'HR','Admin','Engineering'];
	async function updateDepartmemtName(userId,userDepartmentName) {
		const mutation = gql`
		mutation updateDepartmentName($id:Int!, $departmentName:String!) {
  update_users(where: {id: {_eq: $id}}, _set: {departmentName: $departmentName}) {
    affected_rows
  }
}
		`
		client.mutate({
			mutation,
			variables:{
				id: userId,
				departmentName: userDepartmentName
			}
		});
	}
	async function insertUser() {
		const mutation = gql`
		mutation InsertUser($userName:String!, $departmentName:String!) {
  insert_users(objects: {userName: $userName, departmentName: $departmentName}) {
    returning {
      id
    }
    affected_rows
  }
}
		`
		client.mutate({
			mutation,
			variables:{
				userName: user.userName,
				departmentName: user.departmentName
			}
		});
	}
	const query = gql`
	subscription UsersSubscription {
  users {
    departmentName
    id
    userName
  }
}
	`
	const users = client.subscribe({
		query
	})
	const columnDefs = [
		{ field: "id" },
		{ field: "userName" },
		{ 
			field: "departmentName" ,
			cellEditor: 'agSelectCellEditor',
			cellEditorParams: {
				values: departmentNamesAGList,
			},
			onCellValueChanged: (
    			event) => updateDepartmemtName(event.data.id,event.newValue) ,
			editable: true
		}
	];
	const gridOptions = {
		columnDefs: columnDefs,
		rowData: users&& users.data && users.data.users
	};
	let rowData = users && users.data && users.data.users ? users.data.users : [];
	let api=rowData, columnApi = columnDefs;

    const onGridReady = (event) => {
      api = event.api;
      columnApi = event.columnApi;
    };
</script>

<main>
	<form on:submit|preventDefault={insertUser}>
		<input placeholder="Enter User Name" type="text" bind:value={user.userName}/>
		<select placeholder="Enter Department Name" bind:value={user.departmentName}>
			{#each departmentNamesAGList as dname}
				<option value={dname}>
					{dname}
				</option>
			{/each}
		</select>
		<button type="submit">Submit</button>
	</form>
	{#if $users && $users.data && $users.data.users}
	<div style:height="500px" class="ag-theme-alpine">
		<AgGridSvelte bind:rowData={$users.data.users} {columnDefs} {onGridReady}/>
	</div>
	{/if}
</main>

  
<style>
    .ag-theme-alpine {
      --ag-background-color: #ddd;
    }
    .ag-theme-alpine :global(.ag-header-cell-label) {
      font-style: italic;
    }
</style>