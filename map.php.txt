<?php
session_start();
error_reporting(0);
include('includes/dbconnection.php');
    if(isset($_POST["submit"])){
      
        echo $x;
        echo $y;
    }
?>
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Draggable HTML Marker - Azure Maps Web SDK Samples</title>
        
    <meta charset="utf-8" />
	<link rel="shortcut icon" href="/favicon.ico"/>
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    <meta name="description" content="This sample shows how to make an HTML marker draggable." />
    <meta name="keywords" content="Microsoft maps, map, gis, API, SDK, markers, pins, pushpins, symbols, drag, draggable, mouse, GIS, custom, control, custom control, geolocation, user, location, position, tracking, gps, gps tracking" />
    <meta name="author" content="Microsoft Azure Maps" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    <script src="https://azuremapscodesamples.azurewebsites.net/Common/scripts/azure-maps-geolocation-control.min.js"></script>
    <script type='text/javascript'>
         var map, geolocationControl;

        function GetMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add authentication details for connecting to Azure Maps.
                authOptions: {
                    //Use Azure Active Directory authentication.
                    //authType: 'anonymous',
                    //clientId: '04ec075f-3827-4aed-9975-d56301a2d663', //Your Azure Active Directory client id for accessing your Azure Maps account.
                    //getToken: function (resolve, reject, map) {
                    //    //URL to your authentication service that retrieves an Azure Active Directory Token.
                    //    var tokenServiceUrl = "https://azuremapscodesamples.azurewebsites.net/Common/TokenService.ashx";
                    //    fetch(tokenServiceUrl).then(r => r.text()).then(token => resolve(token));
                    //
                    //Alternatively, use an Azure Maps key. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                    authType: 'subscriptionKey',
                    subscriptionKey: 'gaEVY4Ooa2FpTR3MKnq31JdsuPcunWwx6nlgMAF5wog'
                }
            });

                <?php
                    $deactivate=0;
                    $sql="SELECT COUNT(*) FROM tblperson where DEACTIVES='$deactivate' ";
                    $qr= $dbh->prepare($sql);
                    $qr->execute();
                    $result= $qr->fetch(PDO::FETCH_NUM);
                    $count= $result[0];
                    //longitude array
                    $geteachloc="SELECT LONGIT FROM tblperson where DEACTIVES='$deactivate' ORDER BY ID ASC";
                    $geteachqr= $dbh->prepare($geteachloc);
                    $geteachqr->execute();
                    $getres= $geteachqr->fetchAll(PDO::FETCH_COLUMN);
                    
                    //latitude array
                    $geteachlat= "SELECT LATIT FROM tblperson where DEACTIVES='$deactivate' ORDER BY ID ASC";
                    $geteachlatqr = $dbh->prepare($geteachlat);
                    $geteachlatqr->execute();
                    $getreslat= $geteachlatqr->fetchAll(PDO::FETCH_COLUMN);

                    //category array
                    $getcat="SELECT Category FROM tblperson where DEACTIVES='$deactivate' ORDER BY ID ASC";
                    $getcatqr= $dbh->prepare($getcat);
                    $getcatqr->execute();
                    $getcatres= $getcatqr->fetchAll(PDO::FETCH_COLUMN);
                    
                    //name
                    $getname="SELECT Name FROM tblperson where DEACTIVES='$deactivate' ORDER BY ID ASC";
                    $getnameqr= $dbh->prepare($getname);
                    $getnameqr->execute();
                    $getnameres= $getnameqr->fetchAll(PDO::FETCH_COLUMN);
                ?>
            
            //Wait until the map resources are ready.
            
            map.events.add('ready', function () {
                //Create a draggable HTML marker.
                    var lala=1;
                    var marker;
                    var c="<?php echo $count?>";
                    var longit= <?php echo json_encode($getres);?>;
                    var latit= <?php echo json_encode($getreslat);?>;
                    var cat= <?php echo json_encode($getcatres);?>;
                    var name1= <?php echo json_encode($getnameres);?>;
                    datasource= new atlas.source.DataSource();
                    map.sources.add(datasource);
                    //alert(c);
                    while(lala<=c){
                    datasource.add([
                        new atlas.data.Feature(new atlas.data.Point([longit[lala-1], latit[lala-1]]),{
                            name: name1[lala-1],
                            description: cat[lala-1]
                        })
                    ]);

                    symbolLayer = new atlas.layer.SymbolLayer(datasource, null, { iconOptions: {allowOverlap: true}});
                    map.layers.add(symbolLayer);

                    popup = new atlas.Popup({
                    position: [longit[lala-1], latit[lala-1]],
                    pixelOffset: [5, -15]
                });
                    lala=lala+1;
                    }

                    map.events.add('mousemove', closePopup);

                    map.events.add('mousemove', symbolLayer, symbolHovered);
                    map.events.add('touchstart', symbolLayer, symbolHovered);
                map.controls.add([
                    //Optional. Add the map style control so we can see how the custom control reacts.
                    new atlas.control.StyleControl(),

                    //Add the geolocation control to the map.
                    new atlas.control.GeolocationControl({
                        style: 'auto'
                    })
                ], {
                    position: 'top-right'
                });
            });
            }

            function closePopup() {
                popup.close();
            }

        function symbolHovered(e) {
            //Make sure the event occurred on a shape feature.
            if (e.shapes && e.shapes.length > 0) {
                var properties = e.shapes[0].getProperties();

                //Update the content and position of the popup.
                popup.setOptions({
                    //Create the content of the popup.
                    content: `<div style="padding:10px;"><b>${properties.name}</b><br/>${properties.description}</div>`,
                    position: e.shapes[0].getCoordinates(),
                    pixelOffset: [5, -15]
                });

                //Open the popup.
                popup.open(map);
            }
        }
    </script>
</head>
<body onload='GetMap()'>
    <div id="myMap" style="position:relative;width:100%;min-width:290px;height:610px;"></div>
</body>
</html>