<template>
  <div class="container">
    <div class="row">
      <div class="col-md-4 mt-5">
        <h1>Perhitung PPH21</h1>
        <form class="row g-3">
          <div class="col-12">
            <label for="nama" class="form-label">Nama wajib pajak :</label>
            <input type="text" class="form-control" v-model="namaWajibPajak" id="nama" placeholder="Example: Jhon Doe">
          </div>
          <div class="col-12">
            <label for="jenisKelamin" class="form-label">Jenis Kelamin :</label>
            <select class="form-select" v-model="jenisKelamin" aria-label="- Pilih Jenis Kelamin -">
              <option selected>- Pilih Jenis Kelamin -</option>
               <option value="laki-laki">Laki-laki</option>
              <option value="perempuan">Perempuan</option>
            </select>
          </div>
          <div class="col-12">
            <label for="pendapatanPerbulan" class="form-label">Pendapatan Perbulan :</label>
            <input type="number" class="form-control" v-model="pendapatanPerbulan" min="0">
          </div>
          <div class="col-md-6">
            <label for="statusPernikahan" class="form-label">Status Pernikahan :</label>
            <div class="form-check">
              <input class="form-check-input" type="checkbox" v-model="statusPernikahan">
              <label class="form-check-label" for="statusPernikahan">
                {{ statusPernikahan? "Menikah": "Belum Menikah" }}
              </label>
            </div>
          </div>
          <div class="col-md-6">
            <label for="totalTanggungan" class="form-label">Total Tangguang :</label>
            <input type="number" class="form-control" v-model="totalTanggungan" min="0" max="3">
          </div>
          <h4 class="mt-5 mb-1">Pengurangan</h4>
          <div class="col-12">
            <div class="form-check">
              <input class="form-check-input" type="checkbox" v-model="biayaJabatan">
              <label class="form-check-label" for="biayaJabatan">
                Biaya jabatan 5% * Penghasilan bruto
              </label>
            </div>
          </div>   
          <div class="col-12">
            <div class="form-check">
              <input class="form-check-input" type="checkbox" v-model="jht" >
              <label class="form-check-label" for="jht">
                Iuran Jaminan Hari Tua (JHT), 2% dari gaji pokok
              </label>
            </div>
          </div>   
          <div class="col-12">
            <div class="form-check">
              <input class="form-check-input" type="checkbox" v-model="jp" >
              <label class="form-check-label" for="jp">
                Jaminan Pensiun (JP), 1% dari gaji pokok
              </label>
            </div>
          </div> 

          <div class="col-12 mt-5">
            <label for="jenisKelamin">Apakah anda memiliki NPWP ? :</label>
            <div class="form-check mt-2">
              <input class="form-check-input" type="checkbox" v-model="npwp" checked>
              <label class="form-check-label" for="npwp">
                {{npwp? 'Ada NPWP':'Tidak ada NPWP'}}
              </label>
            </div>
            <div class="alert alert-primary mt-3" role="alert">
              <p>Note : Jika wajib pajak tidak memiliki NPWP, maka PPh 21 perlu dikalikan 120%.</p>
            </div>
          </div>  
          <div class="mb-5 d-grid gap-2">
            <button class="btn btn-primary" @click="hitung" type="button">Hitung</button>
          </div>
        </form>  
      </div>
      <div class="col-md-8 ml-5 mt-5">
        <div class="container">
          <div class="row p-3" style="background-color: #F8FBFF;">
            <div class="table-responsive">
              <table class="table align-middle">
                <thead>
                  <tr>
                    <th class="pb-5">Gaji Pokok</th>
                    <td class="pb-5"></td>
                    <td class="text-end pb-5">{{ salary }}</td>
                  </tr>
                  <tr>
                    <th>Penghasilan Bruto</th>
                    <td></td>
                    <td></td>
                  </tr>
                  <tr>
                    <td>1. (iii) Biaya jabatan 5% x {{ salary }}</td>
                    <td class="text-end">{{ positionAllowance }}</td>
                    <td></td>
                  </tr>
                  <tr>
                    <td>2. Iuran Jaminan Hari Tua (JHT), 2% dari gaji pokok</td>
                    <td class="text-end">{{ jumlahJht }}</td>
                    <td></td>
                  </tr>
                  <tr>
                    <td>(iv) Jaminan Pensiun (JP), 1% dari gaji pokok</td>
                    <td class="text-end">{{ jumlahJp }}</td>
                    <td></td>
                  </tr>
                  <tr>
                    <td></td>
                    <td></td>
                    <td class="text-end">({{ totalBruto }})</td>
                  </tr>
                  <tr>
                    <th>Penghasilan neto (bersih) sebulan</th>
                    <td></td>
                    <td class="text-end">{{ netIncomeMonth }}</td>
                  </tr>
                  <tr>
                    <td>(v) Penghasilan neto setahun 12 x {{ netIncomeMonth }}</td>
                    <td></td>
                    <td class="text-end pt-5">{{ annualNetIncome }}</td>
                  </tr>
                  <tr>
                    <td>(vi) PTKP</td>
                    <td class="text-end">{{ ptkp }}</td>
                    <td></td>
                  </tr>
                  <tr>
                    <td></td>
                    <td></td>
                    <td class="text-end">{{ ptkp }}</td>
                  </tr>
                  <tr>
                    <td>Penghasilan Kena Pajak Setahun</td>
                    <td></td>
                    <td class="text-end pt-5">{{ annualTaxableIncome }}</td>
                  </tr>
                  <tr>
                    <td>PPh Terutang 5% x {{ annualTaxableIncome }}</td>
                    <td></td>
                    <td class="text-end">{{ pphToBePaid }}</td>
                  </tr>
                  <tr>
                    <th rowspan="4">PPh Pasal 21 Bulan Juli: {{pphToBePaid}}/12{{ !npwp? '*120': '' }}</th>
                    <td></td>
                    <td class="text-end pt-5">{{ totalPph21 }}</td>
                  </tr>
                </thead>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import { reactive, toRefs } from 'vue'

  export default {
    setup(){
      const pph21 = reactive({
        namaWajibPajak: '',
        statusPernikahan: false,
        jenisKelamin: true,
        pendapatanPerbulan: 0,
        totalTanggungan: 0,
        biayaJabatan: false,
        jht: false,
        jp: false,
        npwp: true, 
      });

      const resultCalculates = reactive({
        salary: 0,
        positionAllowance: 0,
        jumlahJht: 0,
        jumlahJp: 0,
        totalBruto: 0,
        netIncomeMonth: 0,
        annualNetIncome: 0,
        ptkp: 0,
        annualTaxableIncome: 0,
        pphToBePaid: 0,
        totalPph21: 0
      });

      const hitung = () => {
        const payload = {
          "name": pph21.namaWajibPajak,
          "salary": parseInt(pph21.pendapatanPerbulan),
          "positionAllowance": pph21.biayaJabatan,
          "jht": pph21.jht,
          "jp": pph21.jp,
          "isMarried": pph21.statusPernikahan,
          "isHusband": pph21.jenisKelamin == 'perempuan'? false:true,
          "dependant":parseInt(pph21.totalTanggungan),
          "isNPWP": pph21.npwp
        };

        fetch('http://localhost:7000/calculates', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(payload),
        })
        .then(response => response.json())
        .then(res => {
          const { data } = res;
          resultCalculates.salary = formatCurrency(data.salary);
          resultCalculates.positionAllowance = formatCurrency(data.positionAllowance);
          resultCalculates.jumlahJht = formatCurrency(data.jht);
          resultCalculates.jumlahJp = formatCurrency(data.jp);
          resultCalculates.totalBruto = formatCurrency(data.totalBruto);
          resultCalculates.netIncomeMonth = formatCurrency(data.netIncomeMonth);
          resultCalculates.annualNetIncome = formatCurrency(data.annualNetIncome);
          resultCalculates.ptkp = formatCurrency(data.taxRelief);
          resultCalculates.annualTaxableIncome = formatCurrency(data.annualTaxableIncome);
          resultCalculates.pphToBePaid = formatCurrency(data.pphToBePaid);
          resultCalculates.totalPph21 = formatCurrency(data.pph21);
        })
        .catch((error) => {
          console.error('Error:', error);
        });
      }

      const formatCurrency = (nominal) => {
        return new Intl.NumberFormat(['ban', 'id']).format(nominal)
      };

      return {
        ...toRefs(pph21),
        ...toRefs(resultCalculates),
        hitung,
      }
    }
  }
 
</script>
