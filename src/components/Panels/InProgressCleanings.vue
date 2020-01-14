<template>
    <div class="card">
        <div class="card-header bg-info">
            <div class="row">
                <div class="col-lg-6 col-xs-12">
                    <h5>Limpiezas en progreso</h5>
                </div>
                <div class="col-lg-6 col-xs-12 d-flex justify-content-end">
                    <div class="alert alert-success resume-completed" role="alert">
                        <strong>Terminadas: </strong>
                    </div>
                    <div class="alert alert-danger resume-completed" role="alert">
                        <strong>Pendientes: {{datos.length}}</strong>
                    </div>
                    <div class="progress-circle resume-completed d-flex">
                        <div class="navigation_pages">
                            <span @click="navigatePrev"> &#60; </span>
                            <span @click="navigateNext"> &#62; </span>
                        </div>
                        <p>{{currentPage}}/{{pages}}</p>
                    </div>
                </div>
            </div>
        </div>
        <div class="card-body p-3">
                <div class="row justify-content-center">
                    <div v-for="(clean,index) in shownCleanings" :key="index"  class="col-lg-4 col-md-6 col-xs-12 item-work">
                        <div  class="item">
                            <div class="media">
                                <div class="d-flex justify-content-center content-image">
                                    <img src="http://pluspng.com/img-png/download-happy-person-png-images-transparent-gallery-advertisement-275.png"
                                        width="75" height="75"/>
                                </div>
                                <div class="media-body">
                                    <h5 class="mt-0">{{clean.user_staff}}</h5>
                                    <p>Limpieza : <span>{{clean.type_cleaning}}</span></p>
                                    <p>Depa : <span>{{clean.owner_department}}</span></p>
                                </div>
                            </div>
                            <div>
                                {{clean.time}}
                            </div>
                        </div>
                    </div>
            </div>
        </div>
    </div>
</template>

<script>

    export default {
        name: "inProgressPanel",
        components: {

        },
        data() {
            return {
                datos : [],
                currentPage: 1,
                carrousel:true
            }
        },
        async created() {
            await this.$store.dispatch('home/progressCleaning');
            this.datos =this.$store.state.home.inProgress;
        this.changePage();
        for(let i=0; i<=this.datos.length;i++){
            this.datos[i].aux = setInterval(() => {
            this.datos[i].time = this.count(this.datos[i].time);
        }, 1000);
        }
        },
      computed: {
            currentCleanings(){
                return this.$store.state.home.inProgress;
            },
            pages(){
                return Math.ceil(this.datos.length/6);
            },
            shownCleanings(){
                return this.datos.slice(this.currentPage*6-6,this.currentPage*6);
            }
        },
        methods: {
            loadStatus() {
                this.$store.dispatch('home/accessStatusCleaning', this.$parent.auth);
            },
            loadCleanings() {
                this.$store.dispatch('home/progressCleaning', this.$parent.auth);
            },
            navigatePrev() {
                this.currentPage>=2?this.currentPage -=1:this.currentPage=4;
            },
            navigateNext() {
                this.currentPage<4?this.currentPage +=1:this.currentPage=1;
            },
            changePage(){
                //eslint-disable-next-line no-constant-condition
                 const self = this;
                this.intervalid1 = setInterval(() => {
                            self.navigateNext();
                }, 30000);
            },
            count(time){
                var  [hours,mins,secs]= time.split(':')
                hours=parseInt(hours);
                mins=parseInt(mins);
                secs=parseInt(secs);
                secs+=1;
                if(secs==60){
                    secs=0;
                    mins+=1;
                }
                if(mins==60){
                    mins=0;
                    hours+=1;
                }
                if(hours==24){
                    hours=0;
                }
                if(secs<10){
                    secs = "0"+secs;
                }
                return hours+":"+mins+":"+secs;
            }
        }
    }
</script>

<style scoped lang="scss">

</style>
