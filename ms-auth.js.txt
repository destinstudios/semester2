import {createClient} from "https://esm.sh/@supabase/supabase-js@2";
import {MS_SUPABASE_URL,MS_SUPABASE_PUBLISHABLE_KEY} from "./ms-config.js";
export const supabase=createClient(MS_SUPABASE_URL,MS_SUPABASE_PUBLISHABLE_KEY,{auth:{persistSession:true,autoRefreshToken:true,detectSessionInUrl:true}});
export async function requireUser(){const {data,error}=await supabase.auth.getSession();if(error||!data.session){location.href="ms-login.html";return null;}return data.session.user;}
export async function getProfile(id){const {data,error}=await supabase.from("profiles").select("id,full_name,role").eq("id",id).maybeSingle();if(error)console.warn(error.message);return data;}
export async function requireAdmin(){const user=await requireUser();if(!user)return null;const profile=await getProfile(user.id);if(profile?.role!=="admin"){alert("यह page केवल admin के लिए है।");location.href="ms-dashboard.html";return null;}return {user,profile};}
export async function signOut(){await supabase.auth.signOut();location.href="index.html";}
export function wireCommon(){document.getElementById("logout-btn")?.addEventListener("click",signOut);document.getElementById("theme-toggle")?.addEventListener("click",()=>{document.body.classList.toggle("light-mode");localStorage.setItem("ms-theme",document.body.classList.contains("light-mode")?"light":"dark");});if(localStorage.getItem("ms-theme")==="light")document.body.classList.add("light-mode");}
export const esc=v=>String(v??"").replace(/[&<>"']/g,c=>({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"}[c]));
