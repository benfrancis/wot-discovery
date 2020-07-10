# Thing Description for Thing Directory

## Authors
* Ben Francis, Krellian, ben@krellian.com
* Michael Stegeman, Mozilla, mstegeman@mozilla.com

## Abstract
This is a high level proposal for describing a Thing Directory in a WoT Thing Description such that a gateway or directory service could itself be described as a web thing.

## Background
Mozilla's [WebThings Gateway](https://iot.mozilla.org/gateway/) is an open source smart home hub which acts as a Web of Things gateway and currently exposes a directory of things via a [`Things`](https://iot.mozilla.org/wot/#things-resource) endpoint of its REST API, which resolves to a list of Thing Descriptions of the devices the gateway manages. Currently there is no mechanism defined for a client to discover that list.

This proposal describes a mechanism for a Thing Description to link to a directory of other Thing Descriptions, such that a Thing Directory itself can be described by a Thing Description.

## Proposal
### Example

Example Thing Description for a directory:
```json
{
  "title": "My Directory",
  "@context": "https://www.w3.org/2019/wot/td/v1",
  },
  "security": { ... },
  "links": [
    {
      "rel": "directory",
      "href": "/things/",
      "subprotocol": "webthing"
    }
  ]
}
```

### Features
* The Thing Description has a top level link with a `rel` of `directory` which links to a resource that provides a list of links to other Thing Descriptions managed by the thing. The API for managing and searching that list would be defined externally in a specification.

### Usage
1. In the [introduction phase](https://github.com/w3c/wot-discovery/blob/master/proposals/directory.md#introduction-phase-first-contact-mechanism) the top level Thing Description of the gateway/directory can be discovered by a client using an external mechanism like mDNS/DNS-SD broadcasts, Bluetooth beacon, etc.
2. In the [exploration phase](https://github.com/w3c/wot-discovery/blob/master/proposals/directory.md#exploration-phase-directory) a client can get the Thing Description for the directory, which includes a link to a list of Thing Descriptions for the devices it manages
3. The Thing Descriptions of individual devices can be fetched with an HTTP `GET` request on their individual Thing Description URLs

## Open issues
1. How should a client initially authenticate with a directory in order to be authorised to get its list of Thing Descriptions? The Thing Description for the directory can provide security metadata, but a client can only access this metadata if it is already authorised to fetch the Thing Description, rendering it useless. Could the gateway/directory initially serve a stripped-down Thing Description which only contains security metadata?
2. An API needs to be defined for the directoy resource to create, read, update and delete links to Thing Descriptions, to search things, and to notify the client when things are added, removed or updated.
3. What link relation type to use for the directory link? `directory` seems like an obvious choice but it is [proposed elsewhere](http://microformats.org/wiki/rel-directory) to have a slightly different meaning.
