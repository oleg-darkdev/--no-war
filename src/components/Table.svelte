
<script>
  import DataTable, {
    Head,
    Body,
    Row,
    Cell,
    Pagination,
  } from '@smui/data-table';
  import Select, { Option } from '@smui/select';
  import IconButton from '@smui/icon-button';
  import { Label } from '@smui/common';

  let Todo = {
    id: '',
    title: '',
    completed: false,
    userId: 1
  };
  let items = [];
  let rowsPerPage = 10;
  let currentPage = 0;

  $: start = currentPage * rowsPerPage;
  $: end = Math.min(start + rowsPerPage, items.length);
  $: slice = items.slice(start, end);
  $: lastPage = Math.max(Math.ceil(items.length / rowsPerPage) - 1, 0);

  $: if (currentPage > lastPage) {
    currentPage = lastPage;
  }

  if (typeof fetch !== 'undefined') {
    // Slice a few off the end to show how the
    // last page looks when it's not full.
    fetch(
      'https://gist.githubusercontent.com/hperrin/e24a4ebd9afdf2a8c283338ae5160a62/raw/dcbf8e6382db49b0dcab70b22f56b1cc444f26d4/todos.json'
    )
      .then((response) => response.json())
      .then((json) => (items = json.slice(0, 197)));
  }
</script>

<section>
<div class="table-wrap">
<DataTable table$aria-label="Todo list" style="width: 100%;">
  <Head>
    <Row>
      <Cell numeric>ID</Cell>
      <Cell style="width: 100%;">Title</Cell>
      <Cell>Completed</Cell>
      <Cell numeric>User ID</Cell>
    </Row>
  </Head>
  <Body>
    {#each slice as item (item.id)}
      <Row>
        <Cell numeric>{item.id}</Cell>
        <Cell>{item.title}</Cell>
        <Cell>{item.completed ? 'Yes' : 'No'}</Cell>
        <Cell numeric>{item.userId}</Cell>
      </Row>
    {/each}
  </Body>

  <Pagination slot="paginate">
    <svelte:fragment slot="rowsPerPage">
      <Label>Rows Per Page</Label>
      <Select variant="outlined" bind:value={rowsPerPage} noLabel>
        <Option value={10}>10</Option>
        <Option value={25}>25</Option>
        <Option value={100}>100</Option>
      </Select>
    </svelte:fragment>
    <svelte:fragment slot="total">
      {start + 1}-{end} of {items.length}
    </svelte:fragment>

    <IconButton
      class="material-icons"
      action="first-page"
      title="First page"
      on:click={() => (currentPage = 0)}
      disabled={currentPage === 0}>first_page</IconButton
    >
    <IconButton
      class="material-icons"
      action="prev-page"
      title="Prev page"
      on:click={() => currentPage--}
      disabled={currentPage === 0}>chevron_left</IconButton
    >
    <IconButton
      class="material-icons"
      action="next-page"
      title="Next page"
      on:click={() => currentPage++}
      disabled={currentPage === lastPage}>chevron_right</IconButton
    >
    <IconButton
      class="material-icons"
      action="last-page"
      title="Last page"
      on:click={() => (currentPage = lastPage)}
      disabled={currentPage === lastPage}>last_page</IconButton
    >
  </Pagination>
</DataTable>
</div>
</section>

<style>
    .table-wrap {
        width: 100%;
        max-width: 1260px;
        display: flex;
        justify-content: center;
        align-content: center;
        align-items: center;
        padding: 10vh 0;
    }

    section {
        display: flex;
        justify-content: center;
        align-content: center;
        align-items: center;
        background-color: #4F72B6;
    }
</style>