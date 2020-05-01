<template>
  <div>
    <b-field label="First Name">
      <b-input
        required
        :name="`${data.id}-firstName`"
        v-model="firstName"
      ></b-input>
    </b-field>
    <b-field label="Last Name">
      <b-input
        required
        :name="`${data.id}-lastName`"
        v-model="lastName"
      ></b-input>
    </b-field>
    <b-field label="Image Source">
      <b-input
        required
        :name="`${data.id}-imageSourceName`"
        v-model="imageSourceName"
      ></b-input>
    </b-field>
    <img :src="`file:///${data.path}`" alt="" />
  </div>
</template>

<script>
export default {
  props: {
    data: Object,
  },
  data() {
    return {
      firstName: "",
      lastName: "",
      imageName: "",
      imageSourceName: "",
    }
  },
}
</script>

<style lang="scss" scoped></style>
