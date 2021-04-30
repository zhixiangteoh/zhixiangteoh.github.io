---
title: SlateVim
layout: post
show_footer: true
github_link: https://github.com/ridhambhat/slatevim
demo_link: https://dev.d3p5pyu6h7q77o.amplifyapp.com
tags:
  - JavaScript
  - Web
  - React
  - Frontend
  - Backend
  - AWS
  - SlateJS
  - Team
  - Award-winner
  - Competition
  - Hackathon
  - MLH
---

SlateVim is a an online collaborative [Vim](https://www.vim.org/) document editor built with [Slate.js](https://www.slatejs.org/examples/richtext) and [AWS Amplify](https://aws.amazon.com/amplify/). Slate.js was used to develop the frontend [What You See Is What You Get (WYSIWYG)](https://en.wikipedia.org/wiki/WYSIWYG) plaintext editor. The backend was developed using the AWS Amplify serverless [GraphQL query API](https://docs.amplify.aws/lib/graphqlapi/getting-started/q/platform/js) to handle mutations and subscriptions for live collaboration. This project won [top open source project](https://devpost.com/software/slatevim) in the MLH fellowship batch 2 orientation hackathon!

## Inspiration

This project was conceived entirely on the first day of my MLH fellowship. The fellowship started off with an orientation hackathon, and given that I was in the open source track, we were restricted to using specific technologies assigned to us in whatever project we ended up working on. My fellowship pod was assigned these four open source technologies to choose from:
1. [Forem](https://github.com/forem/forem)
2. [Slate.js](https://github.com/ianstormtaylor/slate)
3. [AWS Amplify](https://github.com/aws-amplify)
4. [Adobe API](https://github.com/adobe/pdf-embed-api-samples)

I recently began learning Vim, so when we were assigned to an online WYSIWG library like Slate.js, I came up with the idea of making an online Vim editor. This makes up most of the project—the frontend aspect. To incorporate AWS Amplify, a backend service, I decided to expand this simple idea to an online editor in which your edited data persists across reloads, and on top of that I gained inspiration from [Ben Awad's video](https://www.youtube.com/watch?v=ThN_1Kgald8&ab_channel=BenAwad) on Slate.js collaboration to make collaboration a key feature of the application. When I proposed this to my teammate Ridham, he seemed to like the idea, so we proceeded with it.

## Development

This was our initial development roadmap that I came up with:

![SlateVim development roadmap issue](/images/slatevim_development_roadmap_issue.png)

You can tell that the inspiration mostly spawned from resources I found on the web, and perhaps also that implementation was pretty straightforward. As far as the high-level overview went, it was simple and perfect for a 1-week hackathon project.

### Collaboration

The first two to three days were rough in terms of collaboration because of the 13.5-hour time difference between Ridham and me, but our teamwork later picked up pace, with me working on the main features and Ridham helping to debug and test.

### Implementation

This code snippet shows an example of how we implemented the Normal mode commands. This particular example is for `dd`.

```javascript
function handleDD() {
  setCommand("");
  // Move selection to start of line
  Transforms.move(editor, { unit: "line", reverse: "true" });
  const start = editor.selection.anchor;
  // Move cursor to end of line
  Transforms.move(editor, { unit: "line" });
  const end = editor.selection.anchor;
  // If no more text in editor, don't do anything
  if (start.offset === end.offset) return false;
  // Select text from start of line to end of line
  Transforms.select(editor, {
    anchor: { path: start.path, offset: start.offset },
    focus: { path: end.path, offset: end.offset },
  });
  document.execCommand("cut");
}
```

[Examine source](https://github.com/ridhambhat/SlateVim/blob/main/src/components/NormalEditor.js)

Simply put, the code makes use of the Slate.js `Transforms` API to select the text to cut, then the browser executes `cut` command. For a number of reasons, this is certainly not the best way to implement this. The main reason is that unlike Vim, this method doesn't store the cut text into a dedicated buffer. If you have any suggestions on how to implement this, or would like to, please [contact us](https://github.com/ridhambhat/SlateVim/issues/new)!

---

This next part briefly describes how we implemented the backend. Essentially, we made use of the AWS Amplify CLI to easily spin up a DynamoDB and used the GraphQL query API to retrieve, mutate, and subscribe to our database records, which cut down on all the work of defining endpoints like in a typical [Express.js](https://expressjs.com/en/starter/hello-world.html) REST application.

SlateVim only stores two entries of records, `document` and `operation`, at any one time.

1. `document` the serialized state of the editor
2. `operation` the serialized array of operations pertaining to a single change

For the purposes of this project, we decided to go ahead with serializing our data, for two reasons:

- GraphQL schema does not work easily with dynamic JSON objects (`document`)
- It was much less time-consuming to define the `operation` schema to accept a `String` than an array of clearly-defined Slate.js `Operation` fields

As a reference, these are the `document` and `operation` GraphQL schemas:

```javascript
type Document @model {
  id: ID!
  document: String!
}

type Operation @model {
  id: ID!
  editorId: String!
  op: String!
}
```

[Examine source](https://github.com/ridhambhat/SlateVim/blob/main/amplify/backend/api/slatevim/schema.graphql)

### Challenges

Implementation of the main features were mostly straightforward, yet also tricky. Because we weren't exactly using Slate.js as typical users do (i.e. editing rich text), we came across several problems when trying to implement DOM commands, mostly relating to the way Slate.js handles text nodes and operations on these nodes. One of our major blockers was syncing (and still is) the `insert_node` and `merge_node` operations that result when a user hits `Enter` in the middle of a line of text in collaborative mode, when the user on the receiving end is subscribing to the new series of operations and cannot execute the same operations on their text node.

### Improvements

This is a non-exhaustive list of improvements and bug-fixes that are necessary to make this application actually useable:

- Fix `Enter` and split line bug(s) when collaborating on the same document
- [Fix noticeable lag and latency](https://en.wikipedia.org/wiki/Collaborative_real-time_editor#Technical_challenges) between change made by "sender" and change received by "recipient"
- Implement dedicated buffer and alter all cut, copy, and paste commands to utilize this buffer

## Demo

[This is a link](https://www.youtube.com/watch?v=pngDI7cbyp8&feature=emb_title&ab_channel=zhixiangteoh) to the project demo video submitted for the hackathon.

<!-- ---
{: data-content="footnotes"} -->
