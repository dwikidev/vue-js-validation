<template>
   <div class="input-contact__form-container">
      <h1 data-cy="header-title">{{ title }}</h1>
      <div class="input-contact__form">
         <label for="nama">Nama Lengkap</label>
         <div>
            <input
               data-cy="input-nama"
               v-model="input.full_name"
               type="text"
               name="nama"
               placeholder="Masukkan Nama Lengkap"
            />
         </div>
         <label for="telepon">No. Telepon</label>
         <div>
            <input
               data-cy="input-telepon"
               v-model="input.phone_number"
               type="text"
               name="telepon"
               placeholder="Masukkan Nomor Telepon"
            />
         </div>
         <label for="email">Email</label>
         <div>
            <input
               data-cy="input-email"
               v-model="input.email"
               type="email"
               name="email"
               placeholder="Masukkan Email"
            />
         </div>
         <button
            data-cy="btn-submit"
            :disabled="!input.full_name || !input.phone_number || !input.email"
            @click="onSubmit"
         >
            Simpan
         </button>
      </div>
   </div>
</template>

<script>
// TODO:
// 1. Buat sebuah fungsi yang akan memvalidasi apakah format dari nomor telepon dan email yang dimasukkan sudah benar atau belum
// 2. Jika format nomor telepon salah, maka tampilkan sebuah alert dengan isi pesan "Nomor telepon hanya dapat berupa angka."
// 3. Jika format email salah, maka tampilkan sebuah alert dengan isi pesan "Format email tidak sesuai."
// 4. Jika format nomor telepon dan email sudah benar, maka lanjutkan proses untuk membuat kontak baru atau meng-update kontak

export default {
   name: 'InputContactForm',
   props: {
      title: String,
      selectedContact: Object,
      isEdit: Boolean,
   },
   data() {
      return {
         // eslint-disable-next-line
         // TODO: Uncomment baris kode di bawah untuk membuat regex yang akan membantu memvalidasi format nomor telepon
         regexPhoneNumber: /^[0-9]*$/,
         // eslint-disable-next-line
         // TODO: Uncomment baris kode di bawah untuk membuat regex yang akan membantu memvalidasi format  email
         regexEmail: /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/,
         input: {
            full_name: '',
            phone_number: '',
            email: '',
         },
      };
   },
   watch: {
      isEdit(val) {
         if (val === true) {
            this.input.full_name = this.selectedContact.full_name;
            this.input.phone_number = this.selectedContact.phone_number;
            this.input.email = this.selectedContact.email;
         }
      },
   },
   methods: {
      // method untuk validasi nomor telefon dengan validasi :
      // - harus berupa string dari 0 sampai 9
      // - harus memiliki panjang 12
      // - harus diawali dengan 08*
      validatePhoneNumber() {
         let messageAlert = [];
         // Validasi format nomor telepon menggunakan regex yang sudah disediakan
         if (!this.regexPhoneNumber.test(this.input.phone_number)) {
            messageAlert.push('Nomor telepon hanya dapat berupa angka.');
         }
         // Validasi untuk cek apakah nomor telefon memiliki panjang string 12
         if (this.input.phone_number.length != 12) {
            messageAlert.push(
               'Nomor telepon tidak valid, pastikan nomor telfon memiliki 12 digit.'
            );
         }
         // Validasi untuk cek apakah nomor telefon diawali dengan 08*
         if (this.input.phone_number.substring(0, 2) != '08') {
            messageAlert.push(
               'Nomor telepon tidak valid, pastikan nomor telfon diawali dengan 08'
            );
         }

         // menampilkan beberapa alert jika ada beberapa error yang terjadi ketik validasi tidak memenuhi kriteria
         if (messageAlert.length != 0) {
            for (let message of messageAlert) {
               alert(message);
            }
            return false;
         }
         return true;
      },
      validateEmail() {
         // Validasi format email menggunakan regex yang sudah disediakan
         if (!this.regexEmail.test(this.input.email)) {
            alert('Format email tidak sesuai.');
            return false;
         }
         return true;
      },
      async onSubmit() {
         // Panggil fungsi validasi sebelum submit
         if (!this.validatePhoneNumber() || !this.validateEmail()) {
            return;
         }
         if (this.isEdit) {
            await this.$store.dispatch('updateContactInfo', {
               id: this.selectedContact.id,
               data: {
                  full_name: this.input.full_name,
                  phone_number: this.input.phone_number,
                  email: this.input.email,
               },
            });
         } else {
            await this.$store.dispatch('addNewContact', {
               full_name: this.input.full_name,
               phone_number: this.input.phone_number,
               email: this.input.email,
            });
         }
         this.resetInputValue();
         this.$parent.getAllContactsData();
      },
      resetInputValue() {
         this.input.full_name = '';
         this.input.phone_number = '';
         this.input.email = '';
         this.$parent.resetSelectedData();
      },
   },
};
</script>

<style lang="scss" scoped>
.input-contact {
   &__form-container {
      h1 {
         text-align: center;
      }
   }

   &__form {
      padding-left: 15%;
      padding-right: 15%;

      label {
         font-size: 14px;
         font-weight: bold;
      }

      input {
         margin-top: 4px;
         margin-bottom: 16px;
         width: 98%;
         height: 28px;
      }

      button {
         width: 100%;
         height: 36px;
         border: none;
         border-radius: 4px;
         font-size: 14px;
         font-weight: bold;
         background: #198754;
         color: #ffffff;

         &:disabled {
            opacity: 0.5;
         }
      }
   }
}
</style>
