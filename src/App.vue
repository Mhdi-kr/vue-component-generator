<template>
  <div id="app" style="font-family: Calibri,sans-serif">
    <el-card>
      <div slot="header" class="clearfix">
        <span>Component name and export</span>
      </div>
      <el-row :gutter="20">
        <el-col :span="18">
          <div>
            <el-input clearable placeholder="Component name" v-model="component.name">
              <template slot="append">.vue</template>
            </el-input>
          </div>
        </el-col>
        <el-col :span="4">
          <el-button type="success" round @click="exports">export component</el-button>
        </el-col>
      </el-row>
    </el-card>

    <el-card>
      <div slot="header" class="clearfix">
        <span>Template engine and style settings</span>
      </div>
      <el-row :gutter="20">
        <el-col :span="8">
          <div class="grid-content bg-purple">
            <el-select clearable v-model="component.template" placeholder="template engine">
              <el-option
                  v-for="(item,i) in component.templates"
                  :key="i"
                  :label="item.label"
                  :value="item.label">
              </el-option>
            </el-select>
          </div>
        </el-col>
        <el-col :span="8">
          <div class="grid-content bg-purple">
            <el-select clearable v-model="component.style" placeholder="style">
              <el-option
                  v-for="(item,i) in component.styles"
                  :key="i"
                  :label="item.label"
                  :value="item.label">
              </el-option>
            </el-select>
          </div>
        </el-col>
        <el-col :span="8">
          <div class="grid-content bg-purple">
            <el-checkbox v-model="component.isScoped">scoped style</el-checkbox>
          </div>
        </el-col>
      </el-row>
    </el-card>

    <el-card>
      <div slot="header" class="clearfix">
        <span>Props</span>
        <el-button @click="addProps" style="float: right; padding: 3px 0" type="text"><i class="el-icon-plus"></i> Add new props</el-button>
      </div>
      <div class="data-item" v-for="(item,i) in component.props" :key="i">
        <el-container>
          <el-row :gutter="20">
            <el-col :span="12">
              <el-input clearable @clear="removeProps(i)" :placeholder="`Props #${i}`" v-model="item.key"></el-input>
            </el-col>
            <el-col :span="12">
              <el-select v-if="item.validation.isValidate" clearable v-model="component.template" placeholder="prop type">
                <el-option
                    v-for="(item,i) in component.templates"
                    :key="i"
                    :label="item.label"
                    :value="item.label">
                </el-option>
              </el-select>
            </el-col>
          </el-row>
        </el-container>
        <el-checkbox v-model="item.validation.isValidate">validate this prop</el-checkbox>
        <el-checkbox v-if="item.validation.isValidate" v-model="item.validation.isRequired">required</el-checkbox>
      </div>
    </el-card>

    <el-card>
      <div slot="header" class="clearfix">
        <span>Data</span>
        <el-button @click="addData" style="float: right; padding: 3px 0" type="text"><i class="el-icon-plus"></i> Add new data</el-button>
      </div>
      <div class="data-item" v-for="(item,i) in component.data" :key="i">
        <el-input clearable @clear="removeData(i)" :placeholder="`Data #${i}`" v-model="item.key"></el-input>
      </div>
    </el-card>

    <el-card>
      <div slot="header" class="clearfix">
        <span>Methods</span>
        <el-button @click="addMethod" style="float: right; padding: 3px 0" type="text"><i class="el-icon-plus"></i> Add new method</el-button>
      </div>
      <div class="data-item" v-for="(item,i) in component.methods" :key="i">
        <el-input clearable @clear="removeMethod(i)" :placeholder="`Method #${i}`" v-model="item.key"></el-input>
      </div>
    </el-card>

    <el-card>
      <div slot="header" class="clearfix">
        <span>Computed</span>
        <el-button @click="addComputed" style="float: right; padding: 3px 0" type="text"><i class="el-icon-plus"></i> Add new computed</el-button>
      </div>
      <div class="data-item" v-for="(item,i) in component.computed" :key="i">
        <el-input clearable @clear="removeComputed(i)" :placeholder="`Computed #${i}`" v-model="item.key"></el-input>
      </div>
    </el-card>

    <el-card>
      <div slot="header" class="clearfix">
        <span>Watch</span>
        <el-button @click="addWatch" style="float: right; padding: 3px 0" type="text"><i class="el-icon-plus"></i> Add new watcher</el-button>
      </div>
      <div class="data-item" v-for="(item,i) in component.watch" :key="i">
          <el-input clearable @clear="removeWatch(i)" :placeholder="`Watcher #${i}`" v-model="item.key"></el-input>
      </div>
    </el-card>

    <el-card>
      <div slot="header" class="clearfix">
        <span>lifecycle hooks</span>
      </div>
      <el-switch v-model="component.hook.beforeCreate">beforeCreate</el-switch>
      <el-switch v-model="component.hook.beforeMount">beforeMount</el-switch>
      <el-switch v-model="component.hook.beforeUpdate">beforeUpdate</el-switch>
      <el-switch v-model="component.hook.beforeDestroy">beforeDestroy</el-switch>
      <el-switch v-model="component.hook.created">created</el-switch>
      <el-switch v-model="component.hook.mounted">mounted</el-switch>
      <el-switch v-model="component.hook.updated">updated</el-switch>
      <el-switch v-model="component.hook.destroyed">destroyed</el-switch>
    </el-card>
  </div>
</template>

<script>

const fs = window.require('fs');

export default {
  name: 'App',
  data() {
    return {
      component: {
        name: '',
        templates: [
          {
            label: 'html'
          },
          {
            label: 'pug'
          }
        ],
        styles: [
          {
            label: 'css',
          },
          {
            label: 'scss',
          },
          {
            label: 'sass',
          },
          {
            label: 'stylus',
          },
          {
            label: 'less',
          }
        ],
        style: undefined,
        template: undefined,
        isScoped: false,
        props: [],
        data: [],
        watch: [],
        computed: [],
        methods: [],
        hook: {
          beforeCreate: false,
          beforeMount: false,
          beforeUpdate: false,
          beforeDestroy: false,
          created: false,
          mounted: false,
          updated: false,
          destroyed: false
        }
      }
    }
  },
  methods: {
    removeWatch(i){
      this.component.watch.splice(i,0)
    },
    removeComputed(i){
      this.component.computed.splice(i,1)
    },
    removeData(i){
      this.component.data.splice(i,1)
    },
    removeProps(i){
      this.component.props.splice(i,1)
    },
    removeMethod(i){
      this.component.methods.splice(i,1)
    },
    addMethod(){
      this.component.methods.push({
        key: undefined
      })
    },
    addWatch(){
      this.component.watch.push({
        key: undefined
      })
    },
    addComputed(){
      this.component.computed.push({
        key: undefined
      })
    },
    addData(){
      this.component.data.push({
        key: undefined
      })
    },
    addProps(){
      this.component.props.push({
        key: undefined,
        validation: {
          isValidate: false,
          type: undefined,
          isRequired: undefined
        }
      })
    },
    exports() {
      try {
        fs.writeFileSync(`${this.component.name}.vue`, JSON.stringify(this.component), 'utf-8')
      } catch (err) {
        console.log(err)
      }
    }
  }
}
</script>

<style>
.el-card {
  margin-bottom: 10px;
}
.data-item {
  margin-bottom: 5px;
}
</style>
