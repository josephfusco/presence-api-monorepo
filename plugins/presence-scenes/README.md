# Presence Scenes

Plays real users through probable situations, such as two people editing one post, so the Presence API can be watched and checked end to end. It is a separate plugin that needs Presence API, and it calls only these Presence API functions:

| Function | Used for |
|---|---|
| `wp_get_presence()` | Checking an actor shows up where they should |
| `wp_remove_presence()` | Closing an editor |
| `wp_remove_user_presence()` | Logging out |
| `wp_presence_admin_room()`, `wp_presence_post_room()` | Naming the rooms to check |

Actors reach everything else through core's `heartbeat_received` filter, as a browser would.

## WP-CLI

```
wp presence scene list
wp presence scene run editing-together
wp presence scene stop
```

A run creates a user for each part in the cast, plays each step at its time, then deletes those users and everything they wrote. Runs refuse to overlap, a run whose command died is replaced after 30 seconds, and actors cannot log in. Only the scenes in `library/` can be played.

## Scene format

```json
{
	"apiVersion": 1,
	"name": "editing-together",
	"title": "Editing together",
	"cast": [ "author", "editor" ],
	"steps": [
		{ "at": 0, "actor": 1, "step": "write", "title": "Launch checklist" },
		{ "at": 5, "actor": 2, "step": "open", "post": "Launch checklist" },
		{ "at": 6, "actor": 2, "step": "checkLocked", "post": "Launch checklist" },
		{ "at": 10, "actor": "cast", "step": "leave" }
	]
}
```

| Key | Value |
|---|---|
| `cast` | One to seven roles, each `contributor`, `author` or `editor`; `actor` 1 plays the first |
| `steps` | One to thirty steps |
| `at` | Seconds from the start, 0 to 900, never earlier than the step before |
| `actor` | A part in the cast, or `"cast"` for every part on the steps marked below |
| `post` | The title of a post an earlier `write` created; each `write` needs its own title |

| Step | Fields | Whole cast |
|---|---|---|
| `visit` | `place`: `dashboard`, `posts`, `pages`, `media`, `comments` or `profile` | Yes |
| `write` | `title` | |
| `open` | `post` | |
| `takeOver` | `post` | |
| `type` | `post`, `text` | |
| `close` | `post` | |
| `drop` | | Yes |
| `leave` | | Yes |
| `checkOnline` | | Yes |
| `checkOffline` | | Yes |
| `checkLocked` | `post` | |
| `checkUnlocked` | `post` | |

`title` and `text` are plain text of up to 100 characters.

## Adding a scene

Write a JSON file in `library/` named after its `name`, then run `npm test`. The suite checks every file in `library/`, so a new scene needs no test changes.
