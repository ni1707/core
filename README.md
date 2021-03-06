# OsmSharp

![Build status](http://build.itinero.tech:8080/app/rest/builds/buildType:(id:OsmSharp_CoreDevelop)/statusIcon)

- OsmSharp: [![NuGet](https://img.shields.io/nuget/v/OsmSharp.svg?style=flat)](http://www.nuget.org/profiles/OsmSharp) [![NuGet](https://img.shields.io/nuget/vpre/OsmSharp.svg?style=flat)](http://www.nuget.org/profiles/OsmSharp)
- OsmSharp.Geo: [![NuGet](https://img.shields.io/nuget/v/OsmSharp.Geo.svg?style=flat)](http://www.nuget.org/profiles/OsmSharp.Geo) [![NuGet](https://img.shields.io/nuget/vpre/OsmSharp.Geo.svg?style=flat)](http://www.nuget.org/profiles/OsmSharp.Geo)

[![Visit our website](https://img.shields.io/badge/website-osmsharp.com-020031.svg) ](http://www.osmsharp.com/)  
[![MIT licensed](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/OsmSharp/core/blob/develop/LICENSE.md)  

OsmSharp's core enables you to work directly with OSM-data in .NET/Mono. Most important features are:

- Read/Write OSM-XML.
- Read/Write OSM-PBF.
- Streamed architecture, minimal memory footprint.
- Convert a stream of native OSM objects to 'complete' OSM objects: Ways with all their actual nodes, Relations with all members instantiated.
- Convert OSM objects to geometries.

### Install

    PM> Install-Package OsmSharp
    
There's also a package to use [NTS](https://github.com/NetTopologySuite/) together with OsmSharp to convert OSM-data to features/geometries.

    PM> Install-Package OsmSharp.Geo

### Usage

This readme contains some basic examples, for more documentation and samples check the [wiki](https://github.com/OsmSharp/core/wiki).

A common usecase is to stream and filter OSM data. To read from an OSM file and enumerate all objects just open the file as a stream source and use foreach.

Read data from an OSM-PBF file:

```csharp
// using OsmSharp.Osm.PBF.Streams;

using(var fileStream = new FileInfo(@"/path/to/some/osmfile.osm.pbf").OpenRead())
{
  var source = new PBFOsmStreamSource(fileStream);
  foreach (var element in source)
  {
    Console.WriteLine(element.ToString());
  }
}
```

Write data to an OSM-PBF file:

```csharp
// using OsmSharp.Collections.Tags;
// using OsmSharp.Osm;
// using OsmSharp.Osm.PBF.Streams;

using(var fileStream = new FileInfo(@"/path/to/my/osmfile.osm.pbf").OpenRead())
{
	var target = new PBFOsmStreamTarget(fileStream);
	target.Initialize();
	target.AddNode(new Node()
		{
			Id = 1,
			ChangeSetId = 1,
			Latitude = 0,
			Longitude = 0,
			Tags = new TagsCollection(
				Tag.Create("key", "value")),
			TimeStamp = DateTime.Now,
			UserId = 1424,
			UserName = "you",
			Version = 1,
			Visible = true
		});
	target.Flush();
	target.Close();
}
```

Filter an area and extract a smaller region:

```csharp
// using OsmSharp.Math.Geo;
// using OsmSharp.Osm.PBF.Streams;

var source = new PBFOsmStreamSource(
	new FileInfo(@"/path/to/file.osm.pbf").OpenRead());

var filtered = source.FilterBox(6.238002777099609f, 49.72076145492323f, 
	6.272850036621093f, 49.69928180928878f); // left, top, right, bottom

using (var stream = new FileInfo(@"/path/to/filterede.osm.pbf").Open(FileMode.Create, FileAccess.ReadWrite))
{
   var target = new PBFOsmStreamTarget(stream);
   target.RegisterSource(filter);
   target.Pull();
}
```


### Licensing
The OsmSharp project is licensed under the [MIT license](https://github.com/OsmSharp/core/blob/develop/LICENSE.md).
