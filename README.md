# SmokeSignal Post Encoding Standard

This repo is treated as the canonical resource for how SmokeSignal/Permapost posts are to be encoded and decoded. Currently this only describes the latest version of encoding, version 3.

This standard is in-progress; see elm/Post.elm for an implementation in Elm that is more battle-hardened.

To start with, here's an example SmokeSignal post (with newlines/indents inserted for readability):

```
!smokesignal
{
  "m": [
    "",
    "",
    "It's a bit hard to read your story because there's no punctuation or paragraph breaks. For example, one easy thing to do would have been to separate the subject of your friend that fell off the ladder (Bob I guess?), from that about your poppy.\n\nWhat's the name that's been passed down? I'm also curious about where you live if you'll share it :)"
  ],
  "v": 3,
  "c": {
    "re": [
      16207113,
      "0xce95286bdab09d234ee50b099fe4ff5bc677cfd53192fb267a538179df79e9ad"
    ]
  }
}
```

All SmokeSignal post data is tagged by the prefix `!smokesignal`, followed immediately by a json object.

This is a dictionary object with three keys: m, v, and c.

v ('version'): This is the version of the encoder. Again, this standard assumes version 3.

m ('message'): This is a list of 3 objects in order: title, description, and post body. If the first two are blank, they are considered unset. This message is expected to be written in markdown.

c ('context'): A dictionary with a key of either:
* `re` ('reply'): contains a PostId (see below) to which this post is a reply.
* `topic`: indicates the current post is a "top level" post in some topic. This is expected to be alphanumeric, with optional dashes but no spaces. The SmokeSignal interface does its best to try to enforce this before outright rejecting the topic, considering that we are reading a blockchain of "dirty" data to which anyone can add, i.e. "a new    topic" becomes "a-new-topic".

PostId: a list of two items
* The block at which the message is posted (this is solely to optimise fetching the data - easier for a web3 api provider to fetch a Solidity event if the block is known)
* The hex identifier that can be used to fetch info about the post via the SmokeSignal contract