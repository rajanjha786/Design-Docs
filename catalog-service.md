# Catalog-Service Design docs

## API Spec

| Endpoint      | HTTP Method | Request Body | Status | Response Body | Description                          |
|---------------|-------------|--------------|--------|---------------|--------------------------------------|
| /books        | GET         |              | 200    | Book[]        | Get all books in the catalog         |
| /books        | POST        | Book         | 201    | Book          | Add a new book to catalog            |
|               |             |              | 422    |               | A book with same ISBN already exists |
| /books/{isbn} | GET         |              | 200    | Book          | Get the book with the given ISBN     |
|               |             |              | 404    |               | No book with the give ISBN exists    |
| /books/{isbn} | PUT         | Book         | 200    | Book          | Update the book with the given ISBN  |
|               |             |              | 201    | Book          | Create book with the given ISBN      |
| /books/{isbn} | DELETE      |              | 204    |               | Delete the book with given ISBN      |

