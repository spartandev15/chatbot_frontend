  <template>
    <div style="display: flex; flex-direction: column; align-items: center;">
      <div @click="toggleChatBox" style="text-align: center; position: fixed; width: 80px; height: 80px; bottom: 20px; right: 20px; z-index: 9; transform: scale(0.9); cursor: pointer;">
        <img src="https://i.ibb.co/hRpgKzG/icons8-chatbot-94.png" alt="chat-logo" style="width: 100%; height: 100%;" />
      </div>
  
      <div v-show="isChatOn"
        style="position: fixed; duration: 700ms; bottom: 110px; right: 20px; width: 360px; height: 540px; border-radius: 8px; transition: ease-in; background-color: #dfe9f7; box-shadow: 0 4px 6px rgba(0,0,0,0.1); padding: 8px; z-index: 9;">
        <div id="scrollBar" style="height: 92%; scrollbar-width: none; width: 100%; overflow-x: hidden; overflow-y: scroll;"
          class="scrollBar">
          <div v-for="item in contentLog" :key="item.time">
            <div v-if="item.role === 'assistant'"
              style="display: flex; flex-direction: row; justify-content: flex-start; width: 98%;">
              <div
                style="display: flex; flex-direction: row; justify-content: flex-start; background-color: #f4f7fa; margin-top: 8px; padding: 8px; border-radius: 8px; max-width: 90%; width: fit-content;">
                <div
                  style="display: flex; flex-direction: column; justify-content: flex-start; padding-right: 8px; margin-top: 3px;">
                  <img src="https://i.ibb.co/YPRNMtW/chatbot.png" alt="chatassistant" style="width: 16px; height: auto;" />
                </div>
                <div style="display: flex; flex-direction: column; justify-content: center; max-width: 90%;">
                  <p style="word-break: break-word; font-size: 1rem;">{{ item.content.content }}</p>
                </div>
              </div>
            </div>
  
            <div v-else-if="item.role === 'user'"
              style="display: flex; flex-direction: row; justify-content: flex-end; width: 100%">
              <div
                style="display: flex; flex-direction: row; justify-content: flex-end; background-color: #90EE90; margin-top: 8px; padding: 8px; border-radius: 8px; max-width: 90%; width: fit-content;">
                <div style="display: flex; flex-direction: column; justify-content: center; max-width: 90%;">
                  <p style="word-break: break-word; font-size: 1rem;">{{ item.content }}</p>
                </div>
                <div
                  style="display: flex; flex-direction: column; justify-content: flex-start; padding-left: 8px; margin-top: 3px;">
                  <img src="https://i.ibb.co/WDBsdKF/user.png" alt="chatassistant" style="width: 16px; height: auto;" />
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="msgBox"
          style="position: absolute; display: flex; bottom: 10px; left: 10px; width: 94%; justify-content: space-between;">
          <input @keyup.enter="sendMessage" type="text" placeholder="Ask Chatbot" v-model="message"
            style="width: 98%; background-color: #fff; height: 24px; border-radius: 12px; font-size: 0.8rem; text-align: left; padding: 2px 8px 1px 8px; outline: none; border: none;" />
          <img @click="sendMessage" src="https://i.ibb.co/SRLKxmx/paper-plane.png"
            style="width: 22px; bottom: 10px; right: 10px; margin-left: 5px; text-shadow: 1px 1px 2px #000000;"
            alt="paper-plane" />
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        isChatOn: false,
        answer: '',
        userMessages: [],
        isdateChange: false,
        assistantReply: [],
        message: "",
        isAnswerFromSQL: false,
        contentLog: [],
        systemMsg: `schema has these columns: my_sql_deals_id, last_updated_deals_mysql_record, deal_name, amount, account_name, portfolio_manager_id, project_type, portfolio_manager_name, project_type_description, po_number, project_status, stage, layout, mgmt_company, parent_deal, entity, master_deals_field, sms_opt_out, contact_name, update_deals_field, all_related_zoho_deals, energy_record, property_full_street, mgmt_co_full_street, prop_borough, mgmt_co_apt_suite, prop_state, mgmt_co_city, prop_zip, mgmt_co_state, cd_code, mgmt_co_zip, cd, ll152_year, monday, tuesday, wednesday, thursday, friday, site_contact_phone, tenant_name_and_phone, reinspection_visit_time, mr_co_or_field_tech, field_tech, site_contact_name, site_contact_email, site_contact_ext, client_approved, visit_failure, visit_failure_reason, ll87_year, ll87_sales_start, ll87_proposal_sent, ll87_signed, ll87_checklist_received, ll87_rcx_sent, rcx_report_link, filing_confirmation_link, ll84_133_benchmark, ll87_submitted, ll87_rcx_proof_received, ll87_eercs_received, gps1_sent, gps2_sent, gps2_uploaded, gps2_final_sent, gps2_final_uploaded, gps2_to_lmp, ll152_documentation, ll152_inspection_doc, defects, no_defects, no_gas, at_dispatch_date, visit_window, visit_scheduled, visit_time, visit_confirmed, date_client_responded, dt_client_responded, visit_completed, ll152_invoice_status, ll152_invoice_link, inspection_confirmation_notes, ll84_year, ll84_signed, ll84_checklist_received, ll84_project_link, ll84_invoice_link, ll84_submitted_to_dob, ll69_deal_created, ll69_checklist_received, amount_of_infestations, amount_exterminated, building_unit_count_residential, twentyfour_hour_confidential_phone_number, ll69_filed, dep_pre, dep_post, hpd_cert_sent, hpd_cert_link, hpd_cert_rec, sent_to_hpd, all_violations, mold_visit_day, emergency_notification, hpd_visit_scheduled, hpd_visit_time, hpd_visit_completed, hpd_visit_failure, hpd_visit_failure_reason, story, currency, order_number, apartment, exchange_rate, xrf_deal_created, xrf_amount, xrf_field_tech, xrf_number_of_units_inspected, xrf_internal_project_folder, xrf_inspected_apartments, xrf_visit_completed, xrf_all_data_uploaded, xrf_visit_date, xrf_client_project_folder, class, novid, original_correct_by_date, nov_issued_date, original_certify_by_date, vio_description, new_correct_by_date, new_certify_by_date, section_of_floor, inspection_date, approved_date, proposal, current_status, certified_date, current_status_date, managing_agent_first, managing_agent_last, phone, email, phone_2, email_2, phone_3, email_3, registration_id, bin, units_res, block_field, house_number, lot, borough, certification_box, postcode, gov_facility, county, facility_type, deal_owner, entire_floor, building_id, bldg_class, current_status_id, dof_square_footage, type_deals_field, year_built, phone_1, year_alter_1, knack_deal_id, modified_by, new_workflow_part_2, violation_id, jtg_salesperson, description, redocs_salesperson, violation_status, registration, ll87_site_visit_completed, ll152_filing_confirmation_link, total_mold_sf, deal_type, invoice_balance, invoice_number, invoice_status, invoice_link, zoho_created_time, portal_authorized_users, portal_admin_users, generic_status, project_category, client_action_required, project_type_portal_description, deal_percent_completed, return_inspections_field, project_status_description, active_project_status, portal_read_only_users, hpd_proposal_link, client_affidavit, ll152_filing_opt_out, portal_year_due, ll97_report_sent, ll97_project_folder, id. the table name is 'deals'. generate sql queries for this database schema. be specific and to the point just give query. generate sql query only when user prompt about the data in schema. address user with 'email' data.  retrieve data with respect to my email that is stored in column 'email' and my email is = `
      }
    },
    props: {
      content: { type: Object, required: true },
    },
  
    computed: {
    },
    methods: {
      toggleChatBox() {
        this.isChatOn = !this.isChatOn
      },
      sendMessage() {
        console.log("userId: ", this.content.user)
        const el = document.getElementById("scrollBar")
        if (el) {
          el.scrollTop = el.scrollHeight - 500;
        }
        const date = new Date();
        const time = date.getTime();
        this.userMessages.push({ role: "user", time: time, content: this.message })
        this.message = ""
        this.userMessages.sort((a, b) => a.time - b.time);
        const fetchReply1 = async (systemMsg, query) => {
          this.contentLog = this.userMessages.concat(this.assistantReply);
          this.contentLog.sort((a, b) => a.time - b.time);
          let result = fetch(`https://spartanbots.xyz/test/getResponse?userPrompt=${query}&systemMsg=${systemMsg}`)
            .then((response) => response.json().then((data) => data))
            .catch(error => console.error('Error:', error));
          this.answer = await result
          this.assistantReply.push({ time: time, role: "assistant", content: this.answer })
          this.assistantReply.sort((a, b) => a.time - b.time);
          this.contentLog = this.userMessages.concat(this.assistantReply);
          this.contentLog.sort((a, b) => a.time - b.time);
        }
  
        fetchReply1(this.systemMsg.concat(this.content.user), this.userMessages[this.userMessages.length - 1].content)
      },
    }
  };
  </script>/
  
  <style lang="scss" scoped>
  .my-element {
    html {
      scroll-behavior: smooth;
      font-family: 'Poppins'
    }
  
    input {
      margin-bottom: 50px;
    }
  }
  
  .scrollBar::-webkit-scrollbar {
    width: 0px;
  }
  </style>
  