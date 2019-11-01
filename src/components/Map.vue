<template>
  <div>
    <div id="map"></div>
    <div id="carpark" v-if="!selectedCarPark">
        <h1>Please Select A Car Park Where You Would Like To Advertise</h1>
    </div>
    <div v-else id="carpark-info">
        <div class="deviceinfo" id="deviceinfo">
            <h1>{{ selectedCarPark.name }}</h1>
            <h2>Available devices in this car park:</h2>
            <div v-for="device in selectedCarPark.devices" :key="device.deviceNo" class="device">
                <h3>{{ device.deviceName }}</h3>
                <img :src='"apms/" + device.deviceType + ".jpg"' :alt="device.deviceType">
            </div>
            <a class="btn-next" href="#fileupload">Proceed</a>
        </div>
        <div class="fileupload" id="fileupload">
            <h2>Upload your media</h2>
            <button>File Upload</button>
            <h2>Or input your ad URL:</h2>
            <input type="text" name="url" id="url" placeholder="Ad URL" v-model="url">
            <a class="btn-next" href="#pricing">Proceed</a>
        </div>
        
        <div class="pricing" id="pricing">
            <h1>Choose a pricing plan:</h1>
            <div v-for="pricingplan in selectedCarPark.pricingplans" :key="pricingplan.name" class="pricingplan">
                <h3>{{pricingplan.name}}</h3>
                <h4>{{pricingplan.price}}</h4>
                <p class="period">/ {{pricingplan.period}}</p>
                <p>{{pricingplan.description}}</p>
                <ul v-for="(feature, index) in pricingplan.features" :key="index">
                    <li>{{feature}}</li>
                </ul>
                <button @click="purchaseRequest(user, pricingplan, url, selectedCarPark.name)">Purchase</button>
            </div>
        </div>
    </div>
    <div id="order-success" v-if="orderSuccess">
        <h1>Thank you!</h1>
        <p>Your purchase has been successful.</p>
        <p>Please allow us up to 24h to approve your ad.</p>
        <a class="btn-next" href="#map" @click="resetApp">OK</a>
    </div>
  </div>
</template>

<script>
import gmapsInit from '../utils/gmaps';
import axios from 'axios'
import uniqid from 'uniqid'

export default {
    name: 'Map',
    data: function () {
        return {
            prevInfoWindow: null,
            selectedCarPark: null,
            carparks: null,
            url: null,
            user: {
                name: "John Smith",
                company: "Interparking",
                email: "johnsmith@interparking.com"
            },
            orderSuccess: null
        }
    },
    methods: {
        addMarkers: function (markers, google, map, infoWindow) {
            const that = this;
            markers.forEach(marker => {
                let newMarker = new google.maps.Marker({
                    position: marker.position,
                    map: map,
                    icon: 'marker.png'
                })

                newMarker.setMap(map);
                //Handle marker infoboxes
                google.maps.event.addListener(newMarker, 'click', function(){
                    infoWindow.close();
                    that.selectedCarPark = marker
                    infoWindow.setContent(marker.content);
                    infoWindow.open(map, newMarker)
                })     
            });
        },

        purchaseRequest: function (user, plan, url, cpname) {
            let data = {
                user,
                url,
                plan,
                carpark: cpname,
                status: 0,
                id: uniqid()
            }

            axios.post(`${process.env.VUE_APP_API_BASE}/requestpurchase`, data)
            .then((response) => {
                // eslint-disable-next-line 
                console.log(response)
                this.orderSuccess = true;
                this.selectedCarPark = false;
            }, error => {
                // eslint-disable-next-line 
                console.log(error)
            })
        },

        resetApp: function () {
            this.selectedCarPark = null;
            this.orderSuccess = null;
        }
    },
    async mounted() {
        const google = await gmapsInit();
        const infoWindow = new google.maps.InfoWindow();
        const map = new google.maps.Map(document.getElementById('map'));
        map.setCenter({lat: 47.8095, lng: 13.0550});
        map.setZoom(13)
        map.setOptions({
            streetViewControl: false,
            mapTypeControl: false,
            scrollwheel: false
        })
        

        await axios.get(`${process.env.VUE_APP_API_BASE}/carparks`)
        .then( response => {
            this.carparks = response.data
        })

        this.addMarkers(this.carparks, google, map, infoWindow);
        
    }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#map {
  width: 100%;
  height: 100vh;
  margin: 0 auto;
  background: gray;
}

#carpark-info {
    background: #f5f5f5;
    margin:0 auto;
}

    #carpark-info h1 {
        display:block;
        margin:30px 0 0;
    }

    #carpark-info h2 {
        color: #71787D;
        margin:5px 0 15px;
    }

.deviceinfo {
    height: 100vh;
    background: #fafafa;
    padding:20px;
    margin:0;
}

.device {
    display: inline-block;
    margin: 0 20px 0;
    border: 1px solid #d1d1d1;
    padding:10px;
    background: #ededed;
    box-shadow: 0px 0px 5px rgb(189, 191, 192)
}

    .device:hover {
        background: rgb(247, 247, 247);
    }

    .device img {
        width: 125px;
        height: 125px;
        margin:0;
    }


.pricing {
    text-align:center;
    padding:20px;
    min-height: 100vh;
    background: rgb(210, 228, 230);
}

    .pricingplan {
        display:inline-block;
        width:280px;
        background:#fff;
        margin:20px;
        text-align: center;
        min-height: 90vh;
        vertical-align: top;
        border-radius: 5px;
        box-shadow: 0 0 5px #9ec6d8;
    }

        .pricingplan h3 {
            text-align: center;
            font-weight: bold;
            font-size:22px;
            background: rgb(238, 235, 63);
            margin:0;
            border-top-left-radius: 5px;
            border-top-right-radius: 5px;
            padding:15px;
            color:#333;
        }

        .pricingplan h4 {
            font-size:35px;
            text-align: center;
            margin:30px auto 0;
            color: greenyellow;
        }

        .pricingplan .period {
            text-align: center;
            margin:0;
            font-style: italic;
        }

        .pricingplan p {
            margin:20px;
            font-size: 15px;
            text-align: left;
        }

        .pricingplan ul {
            list-style-type: none;
            margin:0;
            padding:0;
        }
            .pricingplan ul li {
                border-bottom: 1px dotted #d1d1d1;
                padding:5px 20px;
                color:rgb(13, 102, 95);
                text-align: left;
            }

        .pricingplan button {
            padding:10px 20px;
            margin: 15px auto;
            border: 0;
            border-radius: 5px;
            background: #1174e4;
            color:#fff;
            text-transform: uppercase;
            font-weight: bold;
        }

            .pricingplan button:hover {
                background: #0e58ad;
                cursor:pointer;
            }



.fileupload {
    display: flex; 
    flex-direction: column; 
    justify-content: center; 
    align-items: center;
    height: 100vh;
}

    .fileupload input {
        padding:20px;
        border:1px solid #d1d1d1;
        width:75%;
        margin-bottom:30px;
        border-radius: 5px;
    }

    .fileupload button {
        padding:20px 50px;
        border-radius: 5px;
        border:0px;
        background: blue;
        color:#fff;
        margin-bottom:20px;
        cursor: pointer;
    }



.btn-next {
    display:block;
    padding:10px 20px;
    background: #71787D;
    color:#fff;
    text-decoration: none;
    font-weight: bold;
    border-radius: 5px;
    width:200px;
    margin:50px auto 20px;
}



#order-success {
    display: flex; 
    flex-direction: column; 
    justify-content: center; 
    align-items: center;
    height: 100vh;
    background: greenyellow;
}

    #order-success h1 {
        margin:0;
    }

</style>
