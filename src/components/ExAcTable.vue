<template>
  <div class="container-fluid">
    <div class="row">
      <div class="col-md-2"></div>
      <div style="border-radius: 10px; border: solid black 1px" class="col-md-8">
        <table style="text-align:left; width:100%">
          <tr>
            <th>Database</th>
            <th>ID</th>
            <th>Allele Frequency</th>
          </tr>
          <tr v-for="data in table_data">
            <td>{{ data.dataBase }}</td>
            <td>{{ data.identifier }}</td>
            <td>{{ data.allFreq }}</td>
          </tr>
        </table>
      </div>
      <div class="col-md-2"></div>
    </div>
  </div>
</template>
<script type="text/babel">
  import axios from 'axios';

  export default {
    data() {
      return {
        exac_data: [],
        table_data: [],
      };
    },
    props: {
      var_id: {
        type: String,
        required: true,
      },
    },
    methods: {
      hitMyVarient(id) {
        const baseURL = 'http://myvariant.info/v1/query?q=';
        let endpoint = '';
        let finalID = '';
        if (id.includes('dbSNP:')) {
          endpoint = 'dbsnp.rsid:';
          finalID = id.replace('dbSNP:', '');
        }
        if (id.includes('ClinVarVariant:')) {
          endpoint = 'clinvar.allele_id:';
          finalID = id.replace('ClinVarVariant:', '');
        }
        // eslint-disable-next-line
        console.log(baseURL + endpoint + finalID);
        const url = baseURL + endpoint + finalID;

        // eslint-disable-next-line
        axios.get(url)
          .then((resp) => {
            this.exac_data = resp.data.hits;
            // eslint-disable-next-line
            this.exac = this.exac_data[0].exac;
            this.table_data.push(this.parsedbSNP(this.exac_data[0].dbsnp));
            this.table_data.push(this.parseExac(this.exac_data[0].exac));
            this.table_data.push(this.parseWellderly(this.exac_data[0].wellderly));
          })
          .catch((err) => {
            // eslint-disable-next-line
            console.log(err);
          });
      },
      parsedbSNP(block) {
        const dbSNPObj = {};
        dbSNPObj.dataBase = 'dbSNP';
        dbSNPObj.varBase = block.alt;
        dbSNPObj.refBase = block.ref;
        dbSNPObj.position = block.hg19.start;
        dbSNPObj.allFreq = '';
        // eslint-disable-next-line
        block.alleles.forEach(function (obj) {
          if (obj.allele === dbSNPObj.varBase) {
            dbSNPObj.allFreq = obj.freq;
          }
        });
        dbSNPObj.identifier = block.rsid;
        dbSNPObj.url = 'https://www.ncbi.nlm.nih.gov/projects/SNP/snp_ref.cgi?rs=';
        return dbSNPObj;
      },
      parseExac(block) {
        const exacObj = {};
        exacObj.dataBase = 'Exac';
        exacObj.varBase = block.alt;
        exacObj.refBase = block.ref;
        exacObj.position = block.pos;
        exacObj.allFreq = block.af;
        exacObj.identifier = [
          block.chrom,
          block.pos,
          block.ref,
          block.alt,
        ].join('-');
        exacObj.url = 'http://exac.broadinstitute.org/variant/';


        return exacObj;
      },
      parseWellderly(block) {
        const wellObj = {};
        wellObj.dataBase = 'Wellderly';
        wellObj.varBase = block.alt;
        wellObj.refBase = block.ref;
        wellObj.position = block.pos;
        wellObj.allFreq = '';
        // eslint-disable-next-line
        block.alleles.forEach(function (obj) {
          // eslint-disable-next-line
          if (obj.allele === wellObj.varBase) {
            wellObj.allFreq = obj.freq;
          }
        });
        wellObj.identifier = '';
        wellObj.url = 'https://genomics.scripps.edu/browser/';
        return wellObj;
      },
    },
    mounted() {
      this.hitMyVarient(this.var_id);
    },
  };
</script>

