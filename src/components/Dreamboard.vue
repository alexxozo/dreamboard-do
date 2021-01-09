<template>
    <v-container fluid>
        <v-row class="text-center" align="center" justify="center">
            <v-col class="mb-4" cols="8">
                <h1 class="display-2 font-weight-bold my-5">
                    vboard.io 🖼️
                </h1>

            </v-col>
        </v-row>
        <div v-if="!$isMobile()">
            <v-row align="center" justify="center">
                <v-col cols="8" style="text-align:center;">
                    <a style="font-size: 24px; display:block;" target="_blank"
                       href="https://medium.com/journal-of-curiosity-imagination-and-inspiration/create-your-own-vision-board-84b6d5e965f8">What
                        the hell is a visionboard?!?</a>
                    <span style="margin-top: -5px; font-size: 18px;"> <br><strong>tldr;</strong> a collage of images, pictures of one's dreams and desires, <br> designed to serve as a source of inspiration and motivation <br> <strong>aka moving you ass and achieve whatever you put your mind to!</strong></span>
                </v-col>
            </v-row>

            <v-row style="margin-top: 50px;">
                <v-img :src="require('../assets/dreamboard.png')">
                </v-img>
            </v-row>

            <div style="margin-top: 50px; padding-bottom: 20px;">
                <v-row class="mt-5 pt-5" align="center" justify="center">
                    <v-col cols="8">
                        <h1 class="text-center" style="display:block; font-size: 46px;">🚀CREATE YOURS NOW🚀</h1>
                    </v-col>
                </v-row>

                <v-row align="center" justify="center" class="mt-5 pt-5">
                    <v-col
                            class="mb-5"
                            cols="4"
                    >
                        <h2 class="headline font-weight-bold mb-3 text-center">
                            Number of rows: {{rows}}
                        </h2>
                        <v-slider
                                v-model="rows"
                                max="10"
                                min="2"
                        ></v-slider>
                    </v-col>
                </v-row>

                <v-row align="center" justify="center">
                    <v-col
                            class="mb-5"
                            cols="4"
                    >
                        <h2 class="headline font-weight-bold mb-3 text-center">
                            Number of columns: {{cols}}
                        </h2>
                        <v-slider
                                v-model="cols"
                                max="10"
                                min="2"
                        ></v-slider>
                    </v-col>
                </v-row>

                <v-row align="center" justify="center">
                    <v-col cols="4" class="mb-5">
                        <h2 class="headline font-weight-bold mb-3 text-center">
                            Size of board
                        </h2>
                        <v-select
                                :items="resolutions"
                                v-model="selectedResolution"
                        ></v-select>
                        <p class="text-center">
                            *If you want to use it as a desktop background, change this to your screen size, otherwise
                            dont
                            worry about it!
                        </p>
                    </v-col>
                </v-row>

                <v-row align="center" justify="center">
                    <v-btn
                            class="ma-2"
                            :loading="loading"
                            :disabled="loading"
                            color="primary"
                            @click="loader = 'loading'"
                    >
                        create visionboard
                    </v-btn>

                    <v-col cols="12">
                        <div id="dreamboard-container"></div>
                    </v-col>

                    <v-btn
                            color="primary"
                            id="saveAsImage"
                            @click="exportAsImage"
                            v-if="this.konva.stage"
                    >
                        Download board
                    </v-btn>

                    <v-dialog
                            v-model="unsplashDialog"
                            persistent
                            max-width="500"
                            scrollable
                    >
                        <v-card
                                max-height="600"
                        >
                            <v-card-title class="">
                                Image search 🖼️
                            </v-card-title>

                            <v-card-text
                                    class="overflow-y-auto"
                                    v-scroll.self='handleScrollUnsplashDialog'
                            >
                                <v-row>
                                    <v-col
                                            cols="12"
                                    >
                                        <form v-on:submit.prevent="searchUnsplash">
                                            <v-row align="center" justify="center" style="padding: 16px 11px 10px">
                                                <v-text-field
                                                        label="Keyword for image"
                                                        hint="Unsplash is used to fetch beautiful and inspiring images."
                                                        v-model="searchTerm"
                                                        style="margin-right:5px;"
                                                ></v-text-field>
                                                <v-btn type="submit" color="primary">Search</v-btn>
                                            </v-row>
                                        </form>
                                    </v-col>
                                </v-row>

                                <v-row
                                        v-if="this.searchResult.length > 0"
                                >
                                    <v-col
                                            class="d-flex child-flex"
                                            style="flex-direction: column;"
                                            cols="4"
                                            v-for="image in this.searchResult"
                                            @click="selectImage(image)"
                                            :key="image.id"
                                    >
                                        <v-img
                                                :src="image.urls.small"
                                                aspect-ratio="1"
                                                :class="image.selected ? 'selected-image' : ''"
                                        >
                                            <template v-slot:placeholder>
                                                <v-row
                                                        class="fill-height ma-0"
                                                        align="center"
                                                        justify="center"
                                                >
                                                    <v-progress-circular
                                                            indeterminate
                                                            color="grey lighten-5"
                                                    ></v-progress-circular>
                                                </v-row>
                                            </template>
                                        </v-img>
                                        <a
                                                target="_blank"
                                                :href="'https://unsplash.com/@' + image.user.username + '?utm_source=vboard.alexxozo.dev&utm_medium=referral'"
                                                style="color: white;"
                                        >by {{image.user.first_name}} {{image.user.last_name}}</a>
                                    </v-col>
                                </v-row>
                            </v-card-text>

                            <v-card-actions>
                                <v-spacer></v-spacer>
                                <v-btn
                                        text
                                        @click="unsplashDialog = false"
                                >
                                    Cancel
                                </v-btn>
                                <v-btn
                                        text
                                        @click="unsplashDialogDone"
                                >
                                    Done
                                </v-btn>
                            </v-card-actions>
                        </v-card>
                    </v-dialog>
                </v-row>
            </div>

            <v-row align="center" justify="center">
                <v-col cols="8" class="mt-5 pt-5">
                    <h1 class="text-left">Why would I want to create a 'vision board'?</h1>
                    <p class="font-weight-regular mt-2"
                       style="font-size: 22px; text-align: left; line-height: 42px;letter-spacing: 1px;">
                        Setting goals and milestones is a <strong>great practice</strong>, unfortunately more often then
                        not
                        we get so <strong>caught up in our day-to-day activities</strong> we forget our main
                        <strong>vision</strong>.
                        <!-- <br> -->
                        It turns out putting your goals on paper in a visual format can actually help you achieve them.
                        <!-- <br> -->
                        It takes literally around 5 minutes but it can really have an impact on your <strong>day to day
                        decisions</strong>.
                    </p>
                </v-col>

                <v-col cols="8" class="mt-5 pt-5">
                    <h1 class="text-left">OK OK I'll use this, show me how! <a href="https://youtu.be/0R_XJwyuEHs">(video
                        tutorial)</a></h1>
                    <br>
                    <p class="font-weight-regular mt-5"
                       style="font-size: 22px; text-align: left; line-height: 42px;letter-spacing: 1px;">
                        <strong style="font-size: 26px;">First step:</strong> think about your goals (describe them in
                        1-3
                        words)
                        <br>
                        <br>
                        <strong style="font-size: 26px;">Second step:</strong> select number of rows and columns your
                        visionboard, you will need a picture for every goal, how many pics you need? (ex. I have 12
                        goals I
                        need to create a grid with 3 rows and 4 columns or inverse)
                        <br>
                        <br>
                        <strong style="font-size: 26px;">Third step (optional):</strong> select the resultion for the
                        visionboard, in case you want to use it as a desktop image reminding you everyday scrolling
                        facebook
                        won't get you that sport car the choose the resolution for your display (<a target="_blank"
                                                                                                    href="http://whatismyscreenresolution.net">i
                        don't know my resolution</a>)
                        <br>
                        <br>
                        <strong style="font-size: 26px;">Forth step:</strong> customize every slot in the board by
                        clicking
                        the it and searching for a meaninful image
                        <br>
                        <br>
                        <strong style="font-size: 26px;">Fifth step (mandatory):</strong> press the 'Download Board'
                        button
                        placed under your visionboard
                    </p>
                </v-col>


            </v-row>
            <v-row align="center" justify="center" style="margin-top: 50px; padding: 0;">
                <v-col cols="8" class="mb-5 mt-5">
                    <h1>Like the app? Want to know when I'll release some updates? Then leave me your email, I
                        promise,
                        no
                        spam 📮</h1>
                </v-col>
                <v-col cols="8">
                    <v-row justify="center">
                        <v-col cols="10">
                            <v-text-field
                                    ref="email"
                                    dense
                                    v-model="newsletterEmail"
                                    :rules="[rules.email]"
                            ></v-text-field>
                        </v-col>
                        <v-col cols="2">
                            <v-btn
                                    color="primary"
                                    :disabled="rules.email(newsletterEmail) === 'Invalid e-mail.'"
                                    @click="submitNewsletter"
                            >Submit
                            </v-btn>
                        </v-col>
                    </v-row>
                </v-col>
            </v-row>

            <v-row align="center" justify="center">
                <v-col
                        cols="8"
                        v-if="emailReceived === true"
                >
                    <h4 style="color: lightgreen;">Thank you, your email is in good hands!</h4>
                </v-col>
                <v-col
                        cols="8"
                        v-if="emailReceived === false"
                >
                    <h4 style="color: red;">Sorry there seem to be a problem, try again later!</h4>
                </v-col>
            </v-row>
        </div>

        <v-row align="center" justify="center" v-if="$isMobile()" style=" padding: 40px 15px;">
            <h1 style="text-align: center; margin-bottom: 30px">Sorry 😢 <br> the app is not yet compatible with mobile
                browsers</h1>
            <h2 v-if="$isMobile()" style="text-align: center; margin-top: 30px;">Don't worry tho, I will notify you when
                there is a mobile
                version! <a
                        href="https://youtu.be/0R_XJwyuEHs">(desktop version)</a></h2>

            <v-col cols="12">
                <v-text-field
                        ref="email"
                        dense
                        v-model="newsletterEmail"
                        :rules="[rules.email]"
                ></v-text-field>
            </v-col>
            <v-btn
                    color="primary"
                    :disabled="rules.email(newsletterEmail) === 'Invalid e-mail.'"
                    @click="submitNewsletter"
            >Submit
            </v-btn>
        </v-row>

        <v-row class="mt-5 pt-5" style="margin-top: 100px;">
            <v-col cols="12">
                <h4 class="text-center">Made with ❤️ by <a target="_blank"
                                                           href="https://twitter.com/alexgabrielsimi">@alexxozo</a>
                </h4>
                <span class="text-center" style="display: block;"><a target="_blank"
                                                                     href="https://twitter.com/alexgabrielsimi">Twitter</a> | <a
                        target="_blank" href="mailto:simion.alexgabriel@yahoo.com">Email</a> | <a target="_blank"
                                                                                                  href="https://www.linkedin.com/in/alex-gabriel-s-4191a4128/">LinkedIn</a></span>
                <h4 class="text-center"><a href="https://vboard.alexxozo.dev">vboard.alexxozo.dev</a></h4>

            </v-col>
        </v-row>
    </v-container>


</template>

<style>
    .selected-image {
        border: 5px solid #0270ff !important;
    }
</style>

<script>
    import Konva from 'konva';

    function downloadURI(uri, name) {
        var link = document.createElement("a");
        link.download = name;
        link.href = uri;
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
        delete window.link;
    }

    function getCrop(image, size, clipPosition = 'center-middle') {
        const width = size.width;
        const height = size.height;
        const aspectRatio = width / height;

        let newWidth;
        let newHeight;

        const imageRatio = image.width / image.height;

        if (aspectRatio >= imageRatio) {
            newWidth = image.width;
            newHeight = image.width / aspectRatio;
        } else {
            newWidth = image.height * aspectRatio;
            newHeight = image.height;
        }

        let x = 0;
        let y = 0;
        if (clipPosition === 'left-top') {
            x = 0;
            y = 0;
        } else if (clipPosition === 'left-middle') {
            x = 0;
            y = (image.height - newHeight) / 2;
        } else if (clipPosition === 'left-bottom') {
            x = 0;
            y = image.height - newHeight;
        } else if (clipPosition === 'center-top') {
            x = (image.width - newWidth) / 2;
            y = 0;
        } else if (clipPosition === 'center-middle') {
            x = (image.width - newWidth) / 2;
            y = (image.height - newHeight) / 2;
        } else if (clipPosition === 'center-bottom') {
            x = (image.width - newWidth) / 2;
            y = image.height - newHeight;
        } else if (clipPosition === 'right-top') {
            x = image.width - newWidth;
            y = 0;
        } else if (clipPosition === 'right-middle') {
            x = image.width - newWidth;
            y = (image.height - newHeight) / 2;
        } else if (clipPosition === 'right-bottom') {
            x = image.width - newWidth;
            y = image.height - newHeight;
        } else if (clipPosition === 'scale') {
            x = 0;
            y = 0;
            newWidth = width;
            newHeight = height;
        } else {
            console.error(
                new Error('Unknown clip position property - ' + clipPosition)
            );
        }

        return {
            cropX: x,
            cropY: y,
            cropWidth: newWidth,
            cropHeight: newHeight,
        };
    }

    function applyCrop(img, pos, layer) {
        img.setAttr('lastCropUsed', pos);
        const crop = getCrop(
            img.image(),
            {width: img.width(), height: img.height()},
            pos
        );
        img.setAttrs(crop);
        layer.draw();
    }

    function createLayout(rows, columns, width, height, vueContext) {
        var stage = new Konva.Stage({
            container: 'dreamboard-container',
            width: width,
            height: height,
        });
        vueContext.konva.stage = stage;

        const layer = new Konva.Layer();
        stage.add(layer);

        width = width - 4 * columns - 20;
        height = height - 4 * rows - 20;

        let cellHeight = height / rows;
        let cellWidth = width / columns;

        console.log(width, height, cellWidth, cellHeight)

        let matrix = new Array(rows);
        for (let r = 0; r < rows; r++) {
            matrix[r] = new Array(columns);
        }

        let yCoord = 0;
        for (let r = 0; r < rows; r++) {
            let xCoord = 0;
            yCoord += 4;
            for (let c = 0; c < columns; c++) {
                xCoord += 4;
                matrix[r][c] = {
                    url: require('../assets/placeholder.png'),
                    name: "dream",
                    imageAttr: {
                        width: cellWidth,
                        height: cellHeight,
                        x: xCoord,
                        y: yCoord,
                        name: 'image-' + r + '-' + c,
                        draggable: false,
                        listening: true
                    }
                };
                xCoord += cellWidth;
            }
            yCoord += cellHeight;
        }

        for (let r = 0; r < rows; r++) {
            for (let c = 0; c < columns; c++) {
                var imageObj = new Image();
                imageObj.onload = function () {
                    var img = new Konva.Image({
                        image: imageObj,
                        ...matrix[r][c].imageAttr
                    });

                    img.on('click', function () {
                        vueContext.unsplashDialog = true;
                        vueContext.konva.selectedImageName = matrix[r][c].imageAttr.name;
                    });

                    applyCrop(img, 'center-middle', layer);

                    // add the shape to the layer
                    layer.add(img);
                    layer.batchDraw();
                };
                imageObj.src = matrix[r][c].url;
            }
        }

        layer.on('mouseover', function (evt) {
            var shape = evt.target;
            document.body.style.cursor = 'pointer';
            shape.stroke("#0270ff");
            shape.strokeWidth(6);
            layer.draw();
        });

        layer.on('mouseout', function (evt) {
            var shape = evt.target;
            document.body.style.cursor = 'default';
            shape.strokeWidth(0);
            layer.draw();
        });
    }

    export default {
        name: 'Dreamboard',
        props: {},
        data() {
            return {
                rows: 3,
                cols: 3,
                loader: null,
                loading: false,
                unsplashDialog: false,
                searchTerm: '',
                searchResult: [],
                currentPage: 1,
                unsplashDialogOffsetTop: 0,
                imageSelected: null,
                konva: {
                    stage: null,
                    selectedImageName: null,
                    matrix: null
                },
                resolutions: [
                    '1920x1080',
                    '1366x768',
                    '1536x864',
                    '1440x900',
                    '1440x1024',
                    '1280x720',
                    '1600x900'
                ],
                selectedResolution: '1440x900',
                newsletterEmail: null,
                rules: {
                    email: value => {
                        const pattern = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
                        return pattern.test(value) || 'Invalid e-mail.'
                    },
                },
                emailReceived: null
            }
        },
        methods: {
            searchUnsplash(e, pageNumber = 1) {
                console.log("Searching for " + this.searchTerm + " page: " + pageNumber)
                this.currentPage = pageNumber;
                if (pageNumber === 1) {
                    this.searchResult = [];
                    this.imageSelected = null
                }
                fetch(`https://api.unsplash.com/search/photos?page=${pageNumber}&query=${this.searchTerm}&client_id=${process.env.VUE_APP_UNSPLASH_API_KEY}`)
                    .then(result => result.json())
                    .then(result => {
                        let results = result.results.map((obj) => {
                            obj.selected = false;
                            return obj;
                        })
                        this.searchResult = [...this.searchResult, ...results];
                        console.log(this.searchResult);
                    })
            },
            handleScrollUnsplashDialog(e) {
                if (e.target.scrollHeight - e.target.offsetHeight - e.target.scrollTop < 3) {
                    var currentPage = this.currentPage;
                    this.searchUnsplash(null, currentPage + 1)
                }
            },
            selectImage(image) {
                if (this.imageSelected === null) {
                    image.selected = true;
                    this.imageSelected = image;
                } else if (this.imageSelected === image) {
                    this.imageSelected = null;
                    image.selected = false;
                } else if (this.imageSelected !== image && this.imageSelected !== null) {
                    this.imageSelected.selected = false;
                    image.selected = true;
                    this.imageSelected = image;
                }
            },
            async downloadImageUnsplash() {
                await fetch(`${this.imageSelected.links.download_location}&client_id=${process.env.VUE_APP_UNSPLASH_API_KEY}`)
            },
            unsplashDialogDone() {
                if (this.imageSelected) {
                    this.downloadImageUnsplash();
                }

                let konvaLayer = this.konva.stage.getChildren()[0];
                let konvaImage = this.konva.stage.getChildren()[0].findOne('.' + this.konva.selectedImageName);
                var imageObj = new Image();
                imageObj.onload = function () {
                    konvaImage.image(imageObj);
                    applyCrop(konvaImage, 'center-middle', konvaLayer);
                    konvaLayer.draw();
                };
                console.log(this.imageSelected.urls)
                imageObj.src = this.imageSelected.urls.regular;
                imageObj.crossOrigin = 'Anonymous';
                this.searchResult = [];
                this.currentPage = 1;
                this.imageSelected = null;
                this.unsplashDialog = false;
            },
            exportAsImage() {
                var dataURL = this.konva.stage.toDataURL({pixelRatio: 2});
                downloadURI(dataURL, 'visionboard.png');
            },
            submitNewsletter() {
                if (!this.$refs['email'].hasError) {
                    fetch('https://mailchimp-api-server.herokuapp.com/send.php?&email=' + this.newsletterEmail)
                        .then(response => response.json())
                        .then(response => this.emailReceived = response.success);
                }
            }
        },
        watch: {
            loader() {
                const l = this.loader
                this[l] = !this[l]

                // setTimeout(() => (this[l] = false), 500)

                let width = parseInt(this.selectedResolution.split('x')[0]);
                let height = parseInt(this.selectedResolution.split('x')[1]);

                createLayout(this.rows, this.cols, width, height, this);

                // console.log(document.getElementById('dreamboard-container').offsetWidth / 2)
                document.querySelector('.konvajs-content').style.left = document.getElementById('dreamboard-container').offsetWidth / 2 - (document.querySelector('.konvajs-content').offsetWidth / 2) + "px";
                this.loader = null
                this[l] = false
            },
        },
    }
</script>
