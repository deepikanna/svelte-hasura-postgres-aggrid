### Application to dynamically reflect changes in postgresql in web without refreshing using svelte and hasura

- Create a svelte app and run it in a port other than 8080
- Get docker compose file for hasura using following command <br>
wget https://raw.githubusercontent.com/hasura/graphql-engine/stable/install-manifests/docker-compose/docker-compose.yaml
- To run Hasura Graphql engine in docker use following command <br>
docker compose up -d
- Localhost 8080 will now run hasura graphql engine which is connected to postgresql db
- Connect to a new database and create a table "users" in this database
- In hasura localhost create a new mutation by clicking on insert_users and create a new subscription by clicking on subscription and users with all fields
- Use the same query as gql in svelte code
- At the same time install web socket apollo link for creating a graphql subscription or mutation <br>
npm install --save apollo-link-ws subscriptions-transport-ws <br>
- Make sure that queries and mutations will go over HTTP as normal, but subscriptions will be done over the websocket transport
- These are other npm dependencies required for graphql queries used in svelte file <br>
npm install apollo-client apollo-cache-inmemory apollo-link-http apollo-link-error apollo-link graphql-tag graphql <br>
- Install ag-grid-community and ag-grid-svelte for getting AG grid up to display subscribed data
- Create a new mutation to update departmentName for a specific user by clicking update_user with parameters passed
- Make departmentName column editable in AG grid with mutation query being called on every change in a cell in this column



