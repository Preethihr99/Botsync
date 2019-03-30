# Botsync
Web page with map

<html>
<head>
<style>

html,
body,
#googleMap {
  height: 100%;
  width: 100%;
  margin: 0px;
  padding: 0px
}
</style>
<script src="https://maps.googleapis.com/maps/api/js?libraries=geometry"></script>
</head>
<body>
<div id="googleMap"></div>


<script>
function drawCurve(P1, P2, map) {
  var lineLength = google.maps.geometry.spherical.computeDistanceBetween(P1, P2);
  var lineHeading = google.maps.geometry.spherical.computeHeading(P1, P2);
  if (lineHeading < 0) {
    var lineHeading1 = lineHeading + 45;
    var lineHeading2 = lineHeading + 135;
  } else {
    var lineHeading1 = lineHeading + -45;
    var lineHeading2 = lineHeading + -135;
  }
  var pA = google.maps.geometry.spherical.computeOffset(P1, lineLength / 2.2, lineHeading1);
  var pB = google.maps.geometry.spherical.computeOffset(P2, lineLength / 2.2, lineHeading2);

  var curvedLine = new GmapsCubicBezier(P1, pA, pB, P2, 0.01, map);
}

var x = new google.maps.LatLng(14.560774500000008, 121.02631410000006);
var london = new google.maps.LatLng(51.5073509, -0.12775829999998223);
var rcbc = new google.maps.LatLng(14.560648, 121.016781);
var powerplant = new google.maps.LatLng(14.565066, 121.036184);

var trafalgar = new google.maps.LatLng(14.560774500000008, 121.02631410000006);
var mapua = new google.maps.LatLng(14.562883, 121.022039);

function initialize() {
  var mapProp = {
    center: x,
    zoom: 15,
    mapTypeId: google.maps.MapTypeId.ROADMAP
  };

  var map = new google.maps.Map(document.getElementById("googleMap"), mapProp);

  var myTrip = [trafalgar, rcbc];
  var myTrip2 = [rcbc, powerplant];

  drawCurve(trafalgar, rcbc, map);
  drawCurve(rcbc, powerplant, map);

  var marker = new google.maps.Marker({
    position: x,
  });

  var marker2 = new google.maps.Marker({
    position: london,
  });

  var marker3 = new google.maps.Marker({
    position: rcbc,
  });

  var marker4 = new google.maps.Marker({
    position: powerplant,
  });

  marker4.setMap(map);
  marker3.setMap(map);
  marker2.setMap(map);
  marker.setMap(map);
  flightPath.setMap(map);
  flightPath2.setMap(map);
}

google.maps.event.addDomListener(window, 'load', initialize);
// original Belzier Curve code from nicoabie's answer to this question on StackOverflow:
// https://stackoverflow.com/questions/5347984/letting-users-draw-curved-lines-on-a-google-map
var GmapsCubicBezier = function(latlong1, latlong2, latlong3, latlong4, resolution, map) {
  var lat1 = latlong1.lat();
  var long1 = latlong1.lng();
  var lat2 = latlong2.lat();
  var long2 = latlong2.lng();
  var lat3 = latlong3.lat();
  var long3 = latlong3.lng();
  var lat4 = latlong4.lat();
  var long4 = latlong4.lng();

  var points = [];

  for (it = 0; it <= 1; it += resolution) {
    points.push(this.getBezier({
      x: lat1,
      y: long1
    }, {
      x: lat2,
      y: long2
    }, {
      x: lat3,
      y: long3
    }, {
      x: lat4,
      y: long4
    }, it));
  }
  var path = [];
  for (var i = 0; i < points.length - 1; i++) {
    path.push(new google.maps.LatLng(points[i].x, points[i].y));
    path.push(new google.maps.LatLng(points[i + 1].x, points[i + 1].y, false));
  }

  var Line = new google.maps.Polyline({
    path: path,
    geodesic: true,
    strokeColor: "##35495e",
    strokeOpacity: 0.8,
    strokeWeight: 3
      /* dashed line:
          icons: [{
            icon: {
              path: 'M 0,-1 0,1',
              strokeOpacity: 1,
              scale: 4
            },
            offset: '0',
            repeat: '20px'
          }], */
  });

  Line.setMap(map);

  return Line;
};


GmapsCubicBezier.prototype = {

  B1: function(t) {
    return t * t * t;
  },
  B2: function(t) {
    return 3 * t * t * (1 - t);
  },
  B3: function(t) {
    return 3 * t * (1 - t) * (1 - t);
  },
  B4: function(t) {
    return (1 - t) * (1 - t) * (1 - t);
  },
  getBezier: function(C1, C2, C3, C4, percent) {
    var pos = {};
    pos.x = C1.x * this.B1(percent) + C2.x * this.B2(percent) + C3.x * this.B3(percent) + C4.x * this.B4(percent);
    pos.y = C1.y * this.B1(percent) + C2.y * this.B2(percent) + C3.y * this.B3(percent) + C4.y * this.B4(percent);
    return pos;
  }
};
</script>
</body>
</html>
