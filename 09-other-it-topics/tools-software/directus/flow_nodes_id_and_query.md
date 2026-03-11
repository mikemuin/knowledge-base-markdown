# Flow Nodes - ID and Query

When you create a "Read Data" node in Directus Flows, the **IDs** field is used when you want to fetch **specific items** by their primary key (ID).

Here's how it works:

- **To read a single item:** Enter the ID of that item directly. For example, if you want to read the user with ID `123`, you would put `123` in the IDs field.
- **To read multiple specific items:** Enter a comma-separated list of IDs. For example, `1,2,5,10` would fetch items with those IDs.
- **Using a dynamic value:** You can also use Nunjucks templating to pull an ID from a previous operation. For example, `{{ $last.id }}` if the previous operation outputted an ID.

**When to use it vs. the "Query" field:**

- **IDs field:** Use when you know the exact IDs of the items you want to retrieve. It's a direct lookup.
- **Query field:** Use when you want to filter items based on other criteria (e.g., `{"status": {"_eq": "active"}}`, as shown in the immersive). This is for more complex filtering and will return an array of results.

If you leave the IDs field empty, the "Read Data" operation will rely solely on the "Query" field (if configured) or fetch all items if no query is specified (which is generally not recommended for large collections).