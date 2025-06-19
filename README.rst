<input type="email" id="email" class="form-control" placeholder="သင့်အီးမေးလ်ကို ရေးပါ">
                        </div>
                        
                        <div class="form-group">
                            <label for="phone">ဖုန်းနံပါတ်</label>
                            <input type="text" id="phone" class="form-control" placeholder="သင့်ဖုန်းနံပါတ်ကို ရေးပါ">
                        </div>
                        
                        <div class="form-group">
                            <label for="message">စုံစမ်းချက်</label>
                            <textarea id="message" class="form-control" placeholder="သင့်စုံစမ်းချက်ကို ရေးပါ"></textarea>
                        </div>
                        
                        <button type="submit" class="btn">ပေးပို့ရန်</button>
                    </form>
                </div>
            </div>
        </div>
    </section>

    <!-- Footer -->
    <footer>
        <div class="container">
            <div class="logo-small">Xiang Yuan Hotel</div>
            <div class="social-links">
                <a href="#"><i class="fab fa-facebook-f"></i></a>
                <a href="#"><i class="fab fa-twitter"></i></a>
                <a href="#"><i class="fab fa-instagram"></i></a>
                <a href="#"><i class="fab fa-youtube"></i></a>
            </div>
            <div class="copyright">
                <p>&copy; 2025 Xiang Yuan Hotel - အခွင့်အရေးအားလုံးလုံခြုံသည်။</p>
            </div>
        </div>
    </footer>

    <script>
        // Smooth scrolling for navigation links
        document.querySelectorAll('a[href^="#"]').forEach(anchor => {
            anchor.addEventListener('click', function (e) {
                e.preventDefault();
                document.querySelector(this.getAttribute('href')).scrollIntoView({
                    behavior: 'smooth'
                });
            });
        });

        // Header background change on scroll
        window.addEventListener('scroll', function() {
            const header = document.querySelector('header');
            let scrollPosition = window.scrollY;
            
            if(scrollPosition > 100) {
                header.style.backgroundPosition = 'center ' + (-scrollPosition/5) + 'px';
            }
        });
    </script>
</body>
</html>