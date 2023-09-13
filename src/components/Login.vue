<template>
  <v-card>
    <v-card-title>เข้าสู่ระบบ</v-card-title>
    <v-card-text>
      <v-form
        ref="form"
        v-model="valid"
        lazy-validation
      > 
        <v-text-field
          outlined
          v-model="name"
          :counter="20"
          :rules="nameRules"
          placeholder="ชื่อผู้ใช้"
          label="ชื่อผู้ใช้"
          required
        ></v-text-field>

        <v-text-field 
          outlined
          v-model="password"
          :rules="passwordRules"
          placeholder="รหัสผ่าน"
          label="รหัสผ่าน"
          required
        ></v-text-field>

        <v-btn
          :disabled="!valid"
          color="success"
          class="mr-4"
          @click="Login"
          block
        >
          เข้าสู่ระบบ
      
        </v-btn>
      </v-form>
    </v-card-text>
  </v-card>
</template>

<script>
  export default {
    data: () => ({
      valid: true,
      name: '',
      nameRules: [
        v => !!v || 'กรุณากรอกชื่อผู้ใช้',
        v => (v && v.length <= 20) || 'กรุณากรอกชื่อผู้ใช้ไม่เกิน 20 ตัวอักษร',
      ],
      password: '',
      passwordRules: [
        v => !!v || 'กรุณากรอกรหัสผ่าน',
      ]
    
    }),

    methods: {
      Login(){
        if(this.$refs.form.validate(true)) {
          localStorage.setItem('username',this.name)
          this.$EventBus.$emit('getUsername')
          this.$router.push('/')
        }
      }
    }
  }
</script>

<style>

</style>